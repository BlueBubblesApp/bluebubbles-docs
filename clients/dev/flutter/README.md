# Flutter

This section aims to provide extensive documentation and how-tos for all of the complex things we have managed to implement while working within the constraints of a Flutter app. We hope that this can be a useful guide / tool for other developers working on Flutter apps, especially Flutter-based chat apps.

Check the sidebar for all the documentation! **(WIP)**

## Flutter "Review"

As mentioned in [#why-flutter](../../#why-flutter "mention"), we chose Flutter over building something in React Native, or in Java itself. When deciding our framework, we never had full cross platform in mind, and rather we were focusing on the ease of development. While native Android would definitely provide the best overall user experience, we decided to make a small tradeoff on this aspect to utilize Flutter's ease of use, scaleability, and quick turnaround times.

### What We Love

By far and away, the best thing about Flutter has to be the ease of development. Flutter layouting and UI is ridiculously easy to understand, and creating great UI is a simple task. We also really like Dart syntax, it makes code very readable and has a lot of great features to make coding easier.

Pub has been a great package repo - There are so many high quality packages available to drop into your project and eliminate the need for a ton of boilerplate code. We use everything from small UI packages to larger state / framework / dependency mangement packages.

There is definitely much more to like, but it isn't important to list all that here.

### What We Don't Like

We would like to preface this section by saying we are not the most experienced developers in Flutter. Many of the issues listed here might be resolved with advanced code, but so far we haven't been able to find workarounds for them.

One of the biggest issues we face with Flutter is performance and memory usage. Before Flutter 2.8, our app would often surpass 2 GB memory when loading up more than 10-15 JPEGs or MP4s. This functionality is crucial for a messaging app where multimedia is a high priority. We also had many issues with lag in our transitions, animations, and system events such as the keyboard going up and down. Most of the performance issues were resolved with painstaking optimizations in code and the Flutter 2.8 upgrade, however.

Another large issue in Flutter is text input. Out of the box, Flutter doesn't support any sort of image / multimedia paste into textfields. This also means users can't insert GIFs or screenshots from Gboard on Android. To solve this, we ended up creating a fork of the Flutter SDK and engine, which we discuss in more detail at [image-insertion.md](image-insertion.md "mention"). For Web, we ended up using a Dart / JS interop function which is also discussed in the same section.

Our final major downside is just how Flutter interfaces with native. Things like creating notifications, supporting features such as conversation bubbles, or using the default copy/paste menu, are difficult to implement or simply not possible at this time. Many of these issues have open tickets on GitHub but they are either P3, P4, or P5 which doesn't bode well for having any chance these features are added in the near future.

### Conclusion

With all that said, we believe the pros vastly outweigh the cons. Flutter has been a godsend for creating beautiful UI easily, and Dart is a very easy language with great tools to create mobile apps. The package support is also excellent, we love being able to just drop in packages and quickly add new features using them. Above all else, Flutter allowed us to extend not only to mobile, but also Web and Desktop with very minimal changes to our initial mobile-only codebase.

If you are creating a chat app specifically, keep the cons in mind. Be prepared to have to jump through hoops to integrate some important functionalities - however in the end it will be worth it.
