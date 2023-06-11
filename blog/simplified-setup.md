---
description: Our new setup process makes BlueBubbles even more accessible for users
cover: .gitbook/assets/Banner (1).png
coverY: 0
layout: editorial
---

# Simplified Server Setup, Such a Sight to See!

We hear you! Over the past three years, BlueBubbles has grown considerably in feature set, but the setup process has stayed exactly the same. Today, we are fixing that by launching a revamped setup process, complete with Google sign-in integration! This drastically reduces the time and effort needed to setup BlueBubbles. Don't believe us? Check out how short the updated [install guide](https://bluebubbles.app/install) is!

Now, let's delve deeper into what makes this update exciting for both new and existing users.

## Server Update v1.8.0

The star of the show of the new server update is, of course, Google sign in. The server now has the capability of completely automating the Firebase setup process. Just sign in with your Google account, and after a minute or two, you're good to go!

<figure><img src=".gitbook/assets/Google Login" alt=""><figcaption><p>Yeah, it really is that easy!</p></figcaption></figure>

Under the hood, the server uses a "puppet" Google Cloud Project that we created to manage the Firebase projects in your Google account. This allows us to create a Firebase project for you, and automate the necessary steps to ensure everything is configured properly. This is all done through Google's public Cloud Platform REST API. When you authorize our project, it has access to your Google account via a token that is valid for one hour. We do not save or store this token in any way.

Huge thanks to [@probablypablito](https://github.com/probablypablito) for doing the initial research and a proof-of-concept implementation.

If you're not interested in providing your Google account, don't worry, we still support doing the Firebase configuration manually! Please see our updated guide at \<TODO>. We will continue to support manual Firebase configurations in the future, and there will not be any iMessage-parity related differences between Google and Manual setups.

That's not all though, we've got a few other notable features making it to this release.

### CLI Arguments

The server now accepts command line arguments! This allows you to change the server's setup on the fly - you can set _any_ server setting, and these will persist through subsequent launches of the app.

For you automation folks, this means that you can completely bypass the server setup by starting it with CLI arguments. Creating a fully automatic Docker container that spins up a BlueBubbles instance just got a lot easier!

### Headless Mode

The server can now run without displaying anything! You can enable this setting from the server's menu bar icon. Once enabled, the server will close its UI service completely, which makes it utilize less resources - perfect for running on aging hardware or sipping battery on laptops.

<figure><img src=".gitbook/assets/Headless Mode" alt=""><figcaption></figcaption></figure>

### Better Update Handling

The server now has a button in the UI to initiate an update, which should fix one of the pain points with the auto updater not working at times. We also went ahead and added an API endpoint so that clients (or your own integrations) can do this remotely as well.

Besides these notable features, there's a handful of smaller things and bug fixes that were also added. Check out the [release](https://github.com/BlueBubblesApp/bluebubbles-server/releases/tag/v1.8.0) for the full changelog.

### Notable Bug Fixes

* Fixed issues with handling attachments over 200mb
* Fixed a few issues when handling live photos
* Fixed issues surrounding Dynamic DNS URLs

## Client Update v1.12.3

Yet again, the shining star of the client update is also Google sign in! You no longer have to deal with changing QR codes and URLs. Just log in with your Google account, and the app will find your server for you. Enter your server's password, and voila, you're connected.

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Like the server, the client uses the "puppet" Google Cloud Project to read your Firebase projects. We filter out any projects that aren't BlueBubbles related, and show you a list of any projects that have a stored server URL. This is awesome because the client app can find your server even if you made your Firebase project manually! There's no need to have an auto-generated Firebase project.

We're super excited for this feature because it makes it easy to launch BlueBubbles on a brand new device when you don't have easy access to your server URL.

### Notable Features

* Desktop platform now supports the playback of audio and video inline
* FindMy page now supports showing your location in realtime, as well as providing an indicator of the direction you're facing. We hope this makes it easier to locate your position relative to your devices.
* The Android and Desktop apps will now respond to an `imessage://` deep link, so you can quickly start an iMessage chat with someone from a website using them.
* iOS 16.4 Emojis

## Full Changelog

You can view the full changelogs on Github:

{% embed url="https://github.com/BlueBubblesApp/bluebubbles-server/releases/tag/v1.8.0" %}
Server
{% endembed %}

{% embed url="https://github.com/BlueBubblesApp/bluebubbles-app/releases/tag/v1.12.2" fullWidth="false" %}
Client
{% endembed %}

## Get the Update

You can download the server update immediately from [GitHub](https://github.com/BlueBubblesApp/bluebubbles-server/releases/tag/v1.8.0).

You can download the client update immediately from [GitHub](https://github.com/BlueBubblesApp/bluebubbles-app/releases), or it should be available within the next 24 hours in the [Play Store](https://play.google.com/store/apps/details?id=com.bluebubbles.messaging\&hl=en\_US\&gl=US) or Microsoft Store.

## Support Us

If you'd like to support the development of BlueBubbles, you can donate via the link below. It means a lot to us!

{% embed url="https://bluebubbles.app/donate" %}
