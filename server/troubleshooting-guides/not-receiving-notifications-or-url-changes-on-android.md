---
description: >-
  This page will help you troubleshoot and (hopefully) fix issues with not
  receiving notifications or URL changes on the Android app
---

# Not Receiving Notifications or URL Changes on Android

## How are notifications delivered?

Notifications and URL changes are delivered via Google Firebase Messaging (FCM). During the setup of the BlueBubbles Server, you will be given the option to setup a Google Firebase project, linked to your Google account. This allows the BlueBubbles Server to dispatch notifications to your Android device without an open connection to the server. It will also allow the server to write your latest server URL to your Google Firebase's Firestore.

{% hint style="info" %}
If you do not complete the Firebase setup, you will not be able to receive Notifications or URL changes on Android!
{% endhint %}

## I completed the Firebase setup, but still am not receiving notifications or URL changes

If you have completed the Firebase setup, but are still not receiving notifications, it may be caused by a variety of issues. Here are a few:

* You originally set up your phone with a Firebase project, but changed it at a later point (possibly accidentally)
  * Maybe you previously did the manual Firebase setup in the past, and more recently used the new automatic Firebase setup using the `Continue with Google` in the Server.
  * If you have any `sender mismatch ID` errors on your server, that will indicate this case.
* You stopped using the BlueBubbles App for more than 30 days
  * The BlueBubbles Server will clear "inactive" device IDs from its database after 30 days of inactivity
  * Whenever you open the app, your phone will "registers" itself with the server.
* You do not have any devices registered within the `Devices` tab on the BlueBubbles Server
  * This is the first indication that you will not receive notifications
* Google Firebase is being blocked by a VPN, Firewall, Proxy, or DNS Blocker
  * If this is the case, you'll see a `Failed to start FCM Service` error on your server with an error message of  `dial tcp: no route to host`.

## How to fix notifications

Regardless of the issue, here are some ways to fix the issue.

### Check your Mac's internet connection

Sometimes you have a network outage or some other issue. Make sure that your Mac is still connected to the internet.

### Make sure your Mac's time is synced

Sometimes -- especially when using a macOS VM -- your Mac's time can go out of sync. As in, the time does not match up with the actual time in your time zone.

You can check to see if your Mac's time is synced by going to [https://time.is](https://time.is). It will tell you if your time is off, and by how much. It's ok to be a few milliseconds off, however, you do not want to be minutes off.

To fix it, you can open your Mac's `System Preferences` and open `Date & Time` settings. Next, unlock the settings page by typing your password.

If your time does not re-sync after entering your password, you can try changing your time zone temporarily, then changing it back to the original.

{% hint style="info" %}
If you are manually setting your time and time zone, you will likely need to switch to automatic setting of time and time zone.
{% endhint %}

### Check if your VPN, proxy, or DNS blocker is interfering

In some cases, having a VPN, proxy, or DNS blocker may cause interference with registering your device with your Google Firebase project. As a result, you may not be able to register your device with your server, and subsequently, prevent notifications and URL changes to be dispatched to your Android device.

The following apps/tools have been seen to cause issues:

* Blockada
* Adguard
  * [How to use BlueBubbles with Adguard](https://docs.bluebubbles.app/client/usage-guides/using-bluebubbles-with-adguard)
* PiHole
* Certain VPNs

If you are using any of these apps/tools (or similar), and are not able to register your device with the server, disabling them during the BlueBubbles setup may fix the registration issues.

### Re-register your Android device

1. Open your `BlueBubbles Server` and open the `Devices` tab
2. Use the `Manage` drop-down and select `Clear Devices` to remove your registered devices
3. Open your `BlueBubbles App` on Android, and open the Settings
4. Navigaote to the `Connection & Server` page
5. Use the `Re-configure with BlueBubbles Server` option to re-connect your Android device
   * You can also use the `Sign in with Google` button
   * You can also use the `Fetch Firebase Config` button
6. On your `BlueBubbles Server`, use the `Manage -> Refresh` button on the `Devices` tab to see if your device is now registered.

You should now see your new device ID registered with the server, and should start receiving notifications!

### Reboot your Mac

I know... the classic, "have you turned it off and then on again?" line. It may sound odd, but rebooting your Mac can fix a number of issues, not just this issue.

### Reset your BlueBubbles app on Android

Sometimes none of the solutions seem to work. Your next best option is to just reset the Android app.

1. Open your `BlueBubbles App`  on Android and backup your settings & themes using the `Settings -> Backup & Restore` page
2. Navigate back to your Android launcher's home screen
3. Open the BlueBubbles App's settings (through Android)
4. Clear the storage for the app to reset it
5. Open the BlueBubbles App and redo the initial setup & sync

### Install Google Play Services via MicroG (for custom ROMs)

If you are using custom ROM for your Android device, it could just be that you do not have Google Play Services installed. Unfortunately, that is required to receive notifications properly. You will need to install Google Play Services via MicroG.

### If nothing seems to work...

If none of these solutions seem to work, I think the culprit is likely your macOS environment. Make sure to check the BlueBubbles Server's errors/alerts and look for anything to indicate what the culprit might be.

{% hint style="info" %}
For additional help, join the Discord server!
{% endhint %}
