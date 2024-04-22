---
description: >-
  This page will show you how you can restart your server, even when your app
  isn't connected
---

# Restarting Your Server Without a Direct Connection

{% hint style="warning" %}
This will only work if you have Firebase set up with your BlueBubbles Server. Your BlueBubbles Server will also need to be running. If your Mac is offline, shutdown, or logged out, this method will not work.
{% endhint %}

On the off-chance that you are unable to get the BlueBubbles App to connect to your BlueBubbles Server, and you also do not have remote access set up (i.e. TeamViewer or VNC), you can use this guide to force restart your server.

### Steps

**This will restart the BlueBubbles Server, not your Mac itself**

1. Login to your [Firebase Console](https://console.firebase.google.com/)
2. Select/Open the Firebase project linked to your BlueBubbles set up
   * This is likely named something like `bluebubbles-e82d1`, where the part after the dash is randomly generated
3. The next steps will depend upon whether you've used the automated Firebase set up or the manual Firebase setup

{% tabs %}
{% tab title="Automated Firebase Setups" %}
If you used the "Continue with Google" button in the BlueBubbles Server to automate the Firebase project setup, this is what you need to follow.

1. Expand the `Build` section in the left sidebar of your Firebase Console
2. Select `Firestore Database`
3. Select the `server` collection (it should be pre-selected)
4. Select the `commands` document (it should be pre-selected)
5. You should now see a field called `nextRestart` within the fields of `server/commands`
   * **Make a note of the current value of the `serverUrl` field within the `server/config` document**
6. Go to [https://currentmillis.com/](https://currentmillis.com/) and copy the current "milliseconds since EPOCH"
   * It should be a large numeric value such as `1713753987263`
7. Edit the `nextRestart` field and paste the numeric value into it
8. Click `Update` to save the value
{% endtab %}

{% tab title="Manual Firebase Setups" %}
If you've been using BlueBubbles for a long time, this _may be_ how you originally setup BlueBubbles before the automated setup was available. If you did this manually, you had to create a Firebase project, enable the Realtime Database, and download the Firebase configuration files for the BlueBubbles Server.

1. Expand the `Build` section in the left sidebar of your Firebase Console
2. Select `Realtime Database`
3. Select the `config` tree item
4. You should now see a field called `nextRestart`&#x20;
   * **Make a note of the current value of the `serverUrl` field**
5. Go to [https://currentmillis.com/](https://currentmillis.com/) and copy the current "milliseconds since EPOCH"
   * It should be a large numeric value such as `1713753987263`
6. Edit the `nextRestart` field and paste the numeric value into it
7. Deselecting the field should apply the new value
{% endtab %}
{% endtabs %}

Once the `nextRestart` field has been edited, the server will detect the modification, and restart. This restart process may take a couple of minutes, so be patient.

If the BlueBubbles Server has successfully restarted, your `serverUrl` should update with the newly assigned URL (if you are using a proxy service)

### Reasons Why You May Not Get a New Server URL

* Your BlueBubbles Server is not running
* Your Mac Server may be offline or not connected to the internet
* Your home network is blocking connections to Firebase
* Your home network is blocking connections to your proxy service
