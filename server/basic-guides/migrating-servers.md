---
description: >-
  If you want to change the Mac server that your BlueBubbles Server runs on,
  you've come to the right place!
---

# Migrating Servers

{% hint style="info" %}
This guide does not cover redoing the Private API setup. You will need to do that on your own if you want to use those features.
{% endhint %}

1. On your **old** BlueBubbles Mac, backup the following folder:
   * `~/Library/Application\ Support/bluebubbles-server`
2. Open the `Messages` app `Settings` from the macOS status bar.
3. Navigate to the `iMessage` tab
4. Turn on `Enable Messages in iCloud` to backup your messages to iCloud
   * If you do not care to backup your messages to iCloud, skip this step
   * If the option wasn't already enabled, click the `Sync Now` button and give your Mac some time to fully sync the messages (\~24 hrs to be safe)
5. On your **new** BlueBubbles Mac, sign into iMessage and wait for your messages to sync
   * Note: It's always good to confirm you can send iMessages using the native Messages app before proceeding.
6. On your **new** BlueBubbles Mac, copy the `bluebubbles-server` folder that you backed up, back to the  `~/Library/Application\ Support/` folder.
7. Re-download and install the latest BlueBubbles Server from the GitHub [Releases page](https://github.com/BlueBubblesApp/bluebubbles-server/releases)
8. &#x20;Run the BlueBubbles Server
9. If everything is running properly, you can shutdown your **old** Mac

{% hint style="info" %}
**Once this process is complete, make sure you reset your BlueBubbles clients! The underlying macOS database differs from one Mac to another and issues will arise if you do not perform a reset/re-sync on the client-side**
{% endhint %}

If done correctly, your BlueBubbles server should use the same settings from your old Mac, including your Firebase setup, Certificates, etc.

Enjoy!

