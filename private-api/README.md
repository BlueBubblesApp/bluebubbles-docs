---
description: >-
  Info on how the Private API bundle was created, what functions it uses, how to
  contribute, and more.
---

# Intro and Background

## Background

We initially created the helper bundle with the goal of allowing BlueBubbles clients to be able to see and send typing indicators, as well as to send reactions. In recent months the helper project has grown to support the following:

1. Send tapbacks
2. Send and receive typing indicators
3. Mark chats read on the host macOS device
4. Create chats
5. Rename group chats
6. Add or remove participants from group chats
7. Send messages
8. Send replies
9. Send message effects
10. Send mentions

{% hint style="info" %}
Some functions are better supported than others. At the moment, you can only add participants to group chats that you already have an _existing_ conversation with. This same restriction also applies to creating chats, which defeats the purpose and is the reason we continue to use AppleScript to create chats on our client apps.
{% endhint %}

## Supported Devices & Platforms

The bundle has been tested on MacOS 10.13 (High Sierra) - MacOS 12 (Monterey). It could work on higher or lower MacOS versions, but we do not know for sure.

The bundle supports both Intel and Apple Silicon Macs.

{% hint style="warning" %}
Apple Silicon currently requires using beta MacForge, which can be found [here.](https://github.com/MacEnhance/appcast/raw/master/Beta/MacForge/MacForge.1.2.0B1.zip)&#x20;
{% endhint %}

