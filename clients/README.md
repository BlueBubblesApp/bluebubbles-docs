---
description: >-
  Info on how the clients were coded, what features they have, how to
  contribute, and more.
---

# Intro and Background

## Background

All clients are coded in Flutter and Dart. Our first stable release was published June 15th, 2021, after extensive closed alpha and public beta testing since October of 2020. Along this journey we have learned quite a bit about development in Flutter, and we'd like to share that, along with other things, in the form of this documentation.

### Why Flutter?

We were choosing between Flutter and React Native to be our framework for the project. Initially, we ended up going with React Native because most of the developers were familiar with JavaScript and React development.&#x20;

However, React proved to be difficult for us when creating the UI. We wanted our app to look like iMessage, but with a bit of our own twist - this meant a lot of blur, opacity, and bouncy scrolling. At the time, React did not have great support for these types of things, while with Flutter, it was as simple as providing `decoration` to a `Container`, or different `ScrollPhysics` to a scrollable widget.

We did not consider native development because we figured that development would be slow and clunky, even if the app might be more performant and memory efficient - the tradeoff would not be worth it.

### Features

* Send & receive texts, media, and location
* View tapbacks/reactions, stickers, and replies
* Create new chats (macOS 11+ has limited support while macOS 10 has full support)
* View read/delivered timestamps
* Mute or archive conversations
* Robust theming engine
* Choose between an iOS or Android-style interface
* Lots of customizations and options to personalize your experience

#### Private API Features

* Send and receive typing indicators
* Mark chats read on the host macOS device
* Create chats
* Rename group chats
* Add or remove participants from group chats
* Send messages, replies, message effects, mentions, and tapbacks

## Supported Devices and Platforms

### Android

Android 5.0+

Android Auto (GitHub build required at this time)

WearOS (currently in alpha)

### Desktop

Windows 10 / 11 & Linux

### Web

Any modern browser

## Downloads

{% embed url="https://github.com/BlueBubblesApp/bluebubbles-app/releases" %}
All client stable and beta builds can be found on GitHub
{% endembed %}

{% embed url="https://play.google.com/store/apps/details?id=com.bluebubbles.messaging" %}
All Android stable and beta builds can be found on the Play Store
{% endembed %}

{% embed url="https://www.microsoft.com/store/productId/9P3XF8KJ0LSM" %}
All Windows stable and ebta builds can be found on the Microsoft Store
{% endembed %}

{% embed url="https://discord.gg/6nrGRHT" %}
Alpha builds can be found in Discord
{% endembed %}

## Screenshots

<table><thead><tr><th data-type="files">Chat List</th><th data-type="files">Message View</th><th data-type="files">Private API Features</th></tr></thead><tbody><tr><td><a href=".gitbook/assets/image.png">image.png</a></td><td><a href=".gitbook/assets/image (2).png">image (2).png</a></td><td><a href=".gitbook/assets/image (1).png">image (1).png</a></td></tr></tbody></table>

## Contributors

### Main Developers

{% embed url="https://github.com/zlshames" %}
Zach
{% endembed %}

{% embed url="https://github.com/tneotia" %}
Tanay
{% endembed %}

{% embed url="https://github.com/jjoelj" %}
Joel
{% endembed %}

### Other Contributors

We appreciate y'all!

{% embed url="https://github.com/Bricktheworld" %}
Brick
{% endembed %}

{% embed url="https://github.com/SoPat712" %}
Intellijence
{% endembed %}

{% embed url="https://github.com/elliotnash" %}
Elliot
{% endembed %}
