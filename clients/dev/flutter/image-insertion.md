---
description: >-
  How we added the ability to insert images via keyboard or copy/paste into
  Flutter
---

# Image Insertion

## Android

{% embed url="https://github.com/flutter/flutter/issues/20796" %}
Relevant Flutter Issue
{% endembed %}

One of the most crucial requirements for a chat app is to allow users to insert rich content directly from their keyboard. On Android, this can be things such as stickers, GIFs, bitmoji, and even AI-based context-aware items (e.g. the keyboard offering to paste a recent screenshot). This functionality is sorely missing in Flutter, and we were forced to modify the Flutter Engine and Framework ourselves to implement it.

The basic content commit API is described at [https://developer.android.com/guide/topics/text/image-keyboard](https://developer.android.com/guide/topics/text/image-keyboard). The Java code required to interface with Flutter's \`TextInputClient\` was added in the Flutter Engine.

{% embed url="https://github.com/flutter/engine/pull/35619" %}
PR to Flutter Engine
{% endembed %}

Our PR to the Flutter Engine has been merged! The code essentially attaches the `TextInputChannel` to the `contentCommit` API, and sends the file data through the `PlatformChannel` back to the Flutter Framework.

{% embed url="https://github.com/flutter/flutter/pull/110052" %}
PR to Flutter Framework
{% endembed %}

Our PR to the Flutter Framework is currently pending review. The framework code organizes the data received from the `PlatformChannel` into a Dart class, and then this data can be manipulated however the developer chooses.

## Web

Flutter currently does not have any way to detect image content when a paste action is performed on a text field. We implemented this with the great interoperability that Dart and JS have. We created a simple function that uses the `JS` package to call JS code from Dart:

```dart
@JS()
import 'package:js/js.dart';
import 'package:js/js_util.dart';

// Call invokes JavaScript `getPastedImage()`.
@JS('getPastedImage')
external dynamic getClipboardData();

Future<dynamic> getPastedImageWeb() {
  return promiseToFuture(getClipboardData());
}
```

This is called when we detect a CTRL-V event on the text field using a `RawKeyboardListener`. The JS function unfortunately only works in Chrome, but it uses the browser `Clipboard` APIs to get any media content:

```javascript
function checkClipboard() {
  return new Promise(resolve => {
    navigator.permissions.query({ name: "clipboard-read" }).then((result) => {
      // If permission to read the clipboard is granted or if the user will
      // be prompted to allow it, we proceed.
      if (result.state == "granted" || result.state == "prompt") {
        navigator.clipboard.read().then((data) => {
          for (let i = 0; i < data.length; i++) {
            if (!data[i].types.includes("image/png")) {
              resolve(null);
            } else {
              data[i].getType("image/png").then((blob) => {
                resolve(blob);
              });
            }
          }
        });
      } else {
        resolve(null);
      }
    });
  });
}

async function getPastedImage() {
  const result = await checkClipboard();
  return result;
}
```

This function is placed inside the `web/index.html` file as a `<script>`.&#x20;

## Desktop

{% embed url="https://pub.dev/packages/pasteboard" %}
Pasteboard Package
{% endembed %}

We use the pasteboard package on Desktop to get the most recent item (image) from the clipboard. This is also called when we detect a CTRL-V event on the text field using a `RawKeyboardListener`.
