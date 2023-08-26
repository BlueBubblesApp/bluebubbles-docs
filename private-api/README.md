---
description: >-
  Info on how the Private API bundle was created, what functions it uses, how to
  contribute, and more.
---

# Intro and Background

## Background

We initially created the helper bundle with the goal of allowing BlueBubbles clients to be able to see and send typing indicators, as well as to send reactions. Over time, the helper project has grown to support the following:

1. Send reactions
2. Send and receive typing indicators
3. Mark chats read on the server Mac
4. Mark chats unread on the server Mac (requires MacOS 13+)
5. Rename group chats
6. Add / remove participants from group chats
7. Leave group chat
8. Update group chat photo (requires MacOS 11+)
9. Send messages
10. Send replies (requires MacOS 11+)
11. Send message effects
12. Send message with subject
13. Send mentions
14. Update pinned chats on the server Mac (requires MacOS 11, higher versions are currently unsupported)
15. Edit messages (requires MacOS 13+)
16. Unsend messages (requires MacOS 13+)
17. Check user focus status (requires MacOS 12+)
18. Force notify a message (requires MacOS 12+)
19. Retrieve Digital Touch and Handwritten message previews (requires MacOS 11+)
20. Create chats
21. Delete chats
22. Delete messages
23. Check user iMessage and FaceTime status

{% hint style="info" %}
Some functions are better supported than others. At the moment, you can only add participants to group chats that you already have an _existing_ conversation with. This same restriction also applies to creating chats, which defeats the purpose and is the reason we continue to use AppleScript to create chats on our client apps.
{% endhint %}

## Supported Devices & Platforms

The bundle has been tested on MacOS 10.13 (High Sierra) - MacOS 12 (Monterey). It could work on higher or lower MacOS versions, but we do not know for sure.

The bundle supports both Intel and Apple Silicon Macs.

{% hint style="warning" %}
Apple Silicon currently requires using beta MacForge, which can be found [here.](https://github.com/MacEnhance/appcast/raw/master/Beta/MacForge/MacForge.1.2.0B1.zip)
{% endhint %}
