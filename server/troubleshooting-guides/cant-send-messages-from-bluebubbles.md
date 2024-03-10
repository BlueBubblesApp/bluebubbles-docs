---
description: >-
  This guide will show you how you can troubleshoot issues with sending messages
  through BlueBubbles.
---

# Can't Send Messages from BlueBubbles

## Check your Server connection

The first thing you need to do is make sure you are actually connected to your server. If you are not, you will need to resolve that issue first. This can be due to a variety of reasons, but here are some things you can check.

1. Check to see if your BlueBubbles Server is running
2. Check to see if the URL on the BlueBubbles App matches the URL on your BlueBubbles Server dashboard.
   * If it does not, follow [this guide](https://docs.bluebubbles.app/server/troubleshooting-guides/not-receiving-notifications-or-url-changes-on-android) to resolve your issues
3. If you are using Cloudflare, you may be hitting a DNS issue due to a newly provisioned domain
   * To resolve this, try turning off the WiFi on your Android device, then fully closing and re-opening the app.
   * You may switch back to WiFi shortly after. The goal is to flush your DNS cache so that your Android device can "find" your provisioned Cloudflare domain.
4. Check to make sure that you don't have a VPN, proxy, or DNS blocker (PiHole, Blockada, Adguard) interfering with the connection

## Check for popups on your Mac

If you have recently set up your BlueBubbles Server, and you are unable to send messages, it may be due to a missing permission to use the `System Events` on your Mac.

Go to your Mac and check for any popups asking if the BlueBubbles Server can interact with your Mac's `System Events` and allow it.

## Make sure that you can send messages from the native iMessage App

Often times, users cannot send messages from BlueBubbles, because they also cannot send messages from the native iMessage app on the Mac.

To see if this is the case, try and send a message to a Contact via the native iMessage app on your Mac. If it doesn't send, this is also why BlueBubbles cannot send messages. If the message sends, but sends as an SMS (green bubble), this may also be why BlueBubbles cannot send either.

If this is the case, this may indicate a couple of things:

1. Your iCloud/iMessage account h as been flagged as spam
   * To resolve this, you may need to chat with Apple to get your account un-flagged
2. The serial number of your Mac has been flagged as spam.
   * This is typically the case for macOS Virtual Machine deployments
   * To resolve this, you may need to regenerate and set a new serial number, MLB, and ROM in your VM configuration.

## A simple reboot

This may sound dumb, but often times issues with sending can be resolved with a simple macOS restart. Sometimes things can just get out of whack, and a simple restart can fix it.

## Try setting up the Private API

If the other solutions do not apply or do not work, we recommend trying to setup the Private API. We actually recommend setting up the Private API anyways for a variety of reasons (if you are able to).

Apple isn't perfect, and sometimes the Apple Script environment gets corrupted or just temporarily broken. Apple Script is what we use to send messages when the Private API is not enabled. Generally, using Apple Script to send messages is much less reliable than using the Private API.

This is the best solution if a macOS reboot doesn't immediately fix your issue. In addition, you'll see a massive improvement in send-speed and the features you can use with BlueBubbles.

Go to the link below to learn the benefits of using the Private API, and how to set it up.

{% embed url="https://docs.bluebubbles.app/helper-bundle/installation" %}
