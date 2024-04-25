---
description: Set up BlueBubbles without signing into your Google account
---

# Manual Setup

## Server Installation

On the macOS device you'd like to use for the server, open the [server GitHub](https://github.com/BlueBubblesApp/bluebubbles-server/releases/latest). Scroll to the bottom and download the .dmg file.

<figure><img src="../.gitbook/assets/Screenshot 2023-05-02 at 12.46.24 PM (1).png" alt=""><figcaption><p>Download the .dmg file in the Assets list</p></figcaption></figure>

Locate the downloaded .dmg in your Downloads folder in **Finder.** Right click (or Ctrl + Left click) the .dmg file to **Open** the app. (Do not open the .dmg from the Downloads Center in the Dock!)

{% hint style="info" %}
Apple disabled our Developer Account with no further explanation. As a result, the server app is now unsigned. The above process is required to install the unsigned app without visiting the security menus in System Preferences.
{% endhint %}

Drag the app icon to the applications folder when prompted. Right click (or Ctrl + Left click) the popup to **Eject** it. Finally, open the app from the applications folder, and you will be greeted with the welcome screen.

<figure><img src="../.gitbook/assets/Screenshot 2023-05-02 at 12.54.20 PM.png" alt=""><figcaption><p>Welcome screen</p></figcaption></figure>

Proceed through the **Intro**, and **Permissions** steps, following the on-screen guide to enable accessibility and full disk access. Note that accessiblity is **not required** for BlueBubbles to function.

Once full disk access is enabled, proceed to the **Notifications** step. Switch to the **Manual Setup** tab to avoid signing in with Google.

<figure><img src="../.gitbook/assets/Screenshot 2023-05-02 at 12.58.45 PM.png" alt=""><figcaption><p>Select Manual Setup</p></figcaption></figure>

**On your Mac**, open the [Firebase Console](https://console.firebase.google.com/) and login with your Google account. Create a Firebase account and generate `google_services.json` and `firebase-adminsdk.json` by following the below video or written tutorial.

{% tabs %}
{% tab title="Written Guide" %}
{% hint style="info" %}
The name of the project does not have to be **BlueBubblesApp** if you already have a project by that name.
{% endhint %}

1. Click **Create a Project** and enter **BlueBubblesApp** as the name. Disable Google Analytics (why do you want Google spying on you?) and wait for the project to be created.
2. In the tabs on the far left, click **Build** > **Firestore Database.**
3. Next, **Create database** and press **Next > Enable.** You can change the database location if you are not based in North America so it is closer to you.
4. If Cloud Firestore glitches and does not show you the database page, simply refresh the page.
5. In the tabs near the top, click **Rules**
6. Set the rule's condition from `allow read, write: if false;` to `allow read, write: if true;` (Change false to true) and click **Publish**.
7. Click the gear cog in the top left and click **Project Settings**.
8. In the tabs near the top, navigate to **Service Accounts**. Generate a new private key and save this locally. **This will download file 1 / 2 needed for the manual setup.**
9. Next, navigate to the **General** tab.
10. Scroll to the bottom of the page and click the Android icon to add an Android app. Set the package name to `com.bluebubbles.messaging` and leave the other fields blank.
11. Click **Register app**, then **Download google\_services.json**. **This will download file 2 / 2 needed for the manual setup.**
{% endtab %}

{% tab title="Video Guide" %}
{% embed url="https://bluebubbles.app/install/manual-setup.mp4" %}
{% endtab %}
{% endtabs %}

After getting the downloaded files, drag and drop them into the fields shown above.

Proceed to the **Connection** step.

<figure><img src="../.gitbook/assets/Screenshot 2023-05-02 at 4.25.02 PM.png" alt=""><figcaption><p>Connection Step</p></figcaption></figure>

Set a strong server password, and make sure to use the floppy disk icon to save it.

{% hint style="info" %}
In most cases, Cloudflare is the proxy service you should use. If you experience any issues with it, please try the following:

1. Cycle your wifi connection on the **client** device (not the server Mac)
2. Restart the server app to get a new URL and repeat step 1 if necessary
3. Set Cloudflare DNS (`one.one.one.one`) in your phone's Private DNS setting

If these fail, you can switch to Ngrok using the Proxy Service dropdown from the server settings. Ngrok may have increased outages and more rate limiting compared to Cloudflare.
{% endhint %}

Proceed to the **Private API** step.

{% hint style="warning" %}
The Private API setup (along with its requirements) is optional! If you would like to immediately set up the Private API, please follow [our guide](https://docs.bluebubbles.app/private-api/installation).

Do not enable the Private API checkbox unless your requirements are passing and you have completed the setup.
{% endhint %}

Proceed to the **Finish** step. Here, you can customize any server settings to your liking, these are mostly cosmetic.

Be sure to visit any of our Basic or Advanced guides in the sidebar for even further configuration and customization possibilities!

You are now ready to download the app on your Android or PC!

## App Installation

Download and install the app for your desired device from the links on our [website](https://bluebubbles.app/downloads/).

Open the app, making sure to grant all permissions and disable battery optimizations. On the server connection step, you can use the QR code button on the server homepage to quickly connect, or type your URL and password manually. Sync your messages, and you're ready to use BlueBubbles!
