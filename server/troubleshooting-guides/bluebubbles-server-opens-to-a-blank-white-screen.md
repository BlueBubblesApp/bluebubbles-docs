---
description: >-
  This document will show you how you can fix the blank white screen issue when
  opening the BlueBubbles Server
---

# BlueBubbles Server Opens to a Blank White Screen

### What's the problem?

The BlueBubbles Server may open to a blank white screen (no UI). This will prevent you from setting up the server and/or interacting with it.

Here is what the issue would look like:

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

### What's the cause?

This is an environmental issue caused by some sort of conflict between Electron & OpenCore. Electron is the framework the server uses to run, and for whatever reason, some users run into an issue where the server's UI is rendered useless when SIP is disabled.

From what has been reported to us, this only occurs in environments that meet the following criteria:

* macOS Sonoma
* SIP disabled through OpenCore

### How can I fix this?

The fix for this should be pretty straightforward, and involves adding an additional boot-arg to your OpenCore deployed Mac.

In your `config.plist` add the following to your boot arguments under `NVRAM>Add7C436110-AB2A-4BBB-A880-FE41995C9F82`

* `boot-args | string | ipc_control_port_options=0`

Finally, reboot your Mac and the issue should be fixed.

