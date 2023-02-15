---
description: >-
  What we've learned and had to deal with while using ObjectBox as our database
  of choice
---

# ObjectBox

## Intro

Initially, our app used the [`sqflite`](https://pub.dev/packages/sqflite) package as our database (i.e. SQLite). The iMessage database on macOS is an SQLite database, so it made the most sense for our app to emulate the same structure (which it did, we copied the CREATE statements for each table and used them to build our own internal database).

This worked well for the most part, but we hit a major roadblock when we decided to port our app to desktop platforms using Flutter and the [`sqflite_common_ffi`](https://pub.dev/packages/sqflite\_common\_ffi) package - The speeds decreased dramatically. A simple DB call could take upwards of 5-10 seconds, and this was causing significant delay for actions like loading chats, message threads, etc which should normally take up to 1 second.

As a result, we decided to look into alternative databases. We landed on two potential ones: [`sqlite3`](https://pub.dev/packages/sqlite3) and [`objectbox`](https://pub.dev/packages/objectbox). We first tried `sqlite3` to avoid a huge migration of code, but it was the same issue - speeds were too slow on desktop. Then, we moved to `objectbox`. The speed issue was resolved, but we discovered a whole host of other issues that required some hacky workarounds and advanced Dart coding to resolve, and this page will discuss that in depth.

## Web Support

It was very tricky to get our code to compile for web while using ObjectBox. The issue is that it uses some `dart:io` exclusive code, such as `Pointer`s and other things.&#x20;

As a result, we need to create two separate files for the same model, with very slightly different code. Here's a quick example:

<details>

<summary><code>/io/model.dart</code></summary>

```dart
import 'package:objectbox/objectbox.dart';

@Entity()
class ScheduledMessage {
  int? id;
  String? chatGuid;
  String? message;
  int? epochTime;
  bool? completed;

  ScheduledMessage({this.id, this.chatGuid, this.message, this.epochTime, this.completed});
}
```

</details>

<details>

<summary><code>/web/model.dart</code></summary>

```dart
class ScheduledMessage {
  int? id;
  String? chatGuid;
  String? message;
  int? epochTime;
  bool? completed;

  ScheduledMessage({this.id, this.chatGuid, this.message, this.epochTime, this.completed});
}
```

</details>

When importing this model into other files, you want to use the following import directive:

```dart
export 'package:path/to/io/scheduled.dart'
    if (dart.library.html) 'package:path/to/html/scheduled.dart';
```

This ensures that when compiling for web platforms, it does not import ObjectBox libraries, which would prevent the app from compiling.

The same process can be used for any functions that call ObjectBox code, create an `io` version which has the function actually perform a task, and the `web` version is empty:

<details>

<summary><code>/io/functions.dart</code></summary>

```dart
import 'package:objectbox/objectbox.dart';

void openObjectbox() {
    store = await openStore(directory: '/objectbox');
    ...
}
```

</details>

<details>

<summary><code>/web/functions.dart</code></summary>

```dart
void openObjectbox() {}
```

</details>

Then use a similar import directive as above, and if you wish, you can call this function on `io` only by using `if (!kIsWeb) openObjectbox();` in your code.

## Asynchronous API

The other big issue with ObjectBox is a lack of an asynchronous API. If you use synchronous code for larger reads in your app, it could cause heavy jittering and lag on page load. This was a huge problem for us, but thankfully there are two ways to mitigate it.

### Using Dart Isolates

{% hint style="warning" %}
Requires Flutter 2.5 (Dart 2.14) and higher!
{% endhint %}

{% hint style="danger" %}
Do not use this method if you plan to use ObjectBox Relations! **It will not work**!
{% endhint %}

The first thing to do is store a reference to the ObjectBox `Store` object by encoding it into a base64 string:

```dart
prefs.setString("objectbox-reference", base64.encode(store.reference.buffer.asUint8List()));
```

You'll want to do this right after you initialize your `Store` in the app.

Next, let's take an example class:

```dart
@Entity()
class Chat {
   //other parameters here

  static Future<List<Chat>> getChats({int limit = 15, int offset = 0}) async {
     return await compute(getChatsIsolate, [limit, offset, prefs.getString("objectbox-reference")!, fakeNames]);
  }
}
```

As you can see, we are trying to get chats from ObjectBox using a given limit and offset. We use the Flutter `compute` function which handles all the boilerplate code for creating an isolate, sending, and receiving arguments.

`getChatsIsolate` must be a top level function, and is defined like so:

```dart
/// Async method to get chats from objectbox
Future<List<Chat>> getChatsIsolate(List<dynamic> stuff) async {
  /// Pull args from input and create new instances of store and boxes
  store = Store.fromReference(getObjectBoxModel(), base64.decode(stuff[2]).buffer.asByteData());
  attachmentBox = store.box<Attachment>();
  chatBox = store.box<Chat>();
  handleBox = store.box<Handle>();
  messageBox = store.box<Message>();
  return store.runInTransaction(TxMode.read, () {
    /// Query the [chatBox] for chats with limit and offset, prioritize pinned
    /// chats and order by latest message date
    final query = (chatBox.query()
          ..order(Chat_.isPinned, flags: Order.descending)
          ..order(Chat_.latestMessageDate, flags: Order.descending))
        .build();
    query
      ..limit = stuff[0]
      ..offset = stuff[1];
    final chats = query.find();
    query.close();
    return chats;
  });
}
```

We get the arguments from the list sent to the function. The most important part is to **use the same `Store` reference as in the main thread!**

This code will get you the best performance possible in the app, and we highly recommend doing it this way. What if you must absolutely use relations though?

### Using `async_task` Package

{% hint style="info" %}
We only recommend using this method if you absolutely need to use ObjectBox Relations! This method is not as performant, but is still definitely better than using the synchronous API.
{% endhint %}

When using the `async_task` package, the example shown above would look something like this:

```dart
@Entity()
class Chat {
   //other parameters here

  static Future<List<Chat>> getChats({int limit = 15, int offset = 0}) async {
     final task = GetChatAttachments([id!, prefs.getString("objectbox-reference")]);
     return (await createAsyncTask<List<Attachment>>(task)) ?? [];
  }
}
```

And `GetChatAttachments` is defined as follows:

```dart
/// Async method to get chats from objectbox
class GetChats extends AsyncTask<List<dynamic>, List<Chat>> {
  final List<dynamic> stuff;

  GetChats(this.stuff);

  @override
  AsyncTask<List<dynamic>, List<Chat>> instantiate(List<dynamic> parameters, [Map<String, SharedData>? sharedData]) {
    return GetChats(parameters);
  }

  @override
  List<dynamic> parameters() {
    return stuff;
  }

  @override
  FutureOr<List<Chat>> run() {
    return store.runInTransaction(TxMode.read, () {
      /// Query the [chatBox] for chats with limit and offset, prioritize pinned
      /// chats and order by latest message date
      final query = (chatBox.query()
        ..order(Chat_.isPinned, flags: Order.descending)
        ..order(Chat_.latestMessageDate, flags: Order.descending))
          .build();
      query
        ..limit = stuff[0]
        ..offset = stuff[1];
      final chats = query.find();
      query.close();
      return chats;
    });
  }
}
```

Finally, we also must add the `createAsyncTask` function:

```dart
/// Create a "fake" asynchronous task from a traditionally synchronous task
///
/// Used for heavy ObjectBox read/writes to avoid causing jank
Future<T?> createAsyncTask<T>(AsyncTask<List<dynamic>, T> task) async {
  final executor = AsyncExecutor(parallelism: 0, taskTypeRegister: () => [task]);
  executor.logger.enabled = true;
  executor.logger.enabledExecution = true;
  await executor.execute(task);
  return task.result;
}
```

The `parallelism: 0` is very important here. It makes sure the `async_task` package doesn't try to use an isolate (which would break due to the Relations), and instead use an async zone to run the task.

### Tricks to Eliminate Jank

Oftentimes jank will happen on animations - you will see dropped frames or stutters. To solve this, there are some things you should check and try doing:

* Use transactions to group larger reads / writes into a single block
* Reduce the number of reads / writes by simplifying your code
* Run database code after page animations are complete:

```dart
//run inside initState
WidgetsBinding.instance!.addPostFrameCallback((timeStamp) {
  if (ModalRoute.of(context)?.animation != null) {
    if (ModalRoute.of(context)?.animation?.status != AnimationStatus.completed) {
      late final AnimationStatusListener listener;
      listener = (AnimationStatus status) {
        if (status == AnimationStatus.completed) {
          fetchAttachments();
          ModalRoute.of(context)?.animation?.removeStatusListener(listener);
        }
      };
      ModalRoute.of(context)?.animation?.addStatusListener(listener);
    } else {
      fetchAttachments();
    }
  } else {
    fetchAttachments();
  }
});
```

## Using ObjectBox inside a Background Isolate

You may have a background isolate that runs Dart code when an external event happens, e.g. when you receive an FCM notification. This is the proper way to use the `Store` inside your isolate:

```dart
String? storeRef = prefs.getString("objectbox-reference");
if (storeRef != null) {
  debugPrint("Opening ObjectBox store from reference");
  try {
    store = Store.fromReference(getObjectBoxModel(), base64.decode(storeRef).buffer.asByteData());
  } catch (_) {
    debugPrint("Failed to open store from reference, opening from path");
    store = await openStore(directory: documentsDirectory.path + '/objectbox');
  }
} else {
  debugPrint("Opening ObjectBox store from path");
  store = await openStore(directory: documentsDirectory.path + '/objectbox');
}
```

Every time you initialize a new `Store`, you should store the reference inside some sort of SharedPreferences, like so:

```dart
prefs.setString("objectbox-reference", base64.encode(store.reference.buffer.asUint8List()));
```

This ensures that your code retains concurrency if your app happens to be active but also receives a background event that starts the secondary isolate.
