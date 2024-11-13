---
description: >-
  This page will show you how you can set up Unified Push to receive
  notifications so you don't have to use Google Firebase or the Background
  Service
---

# Using Unified Push for Notifications

Unified Push is an open-source protocol that enables users to choose how they receive push notifications, breaking the dependency on proprietary services. It empowers users by giving them control over their notifications, promoting privacy, and allowing for a more decentralized and customizable notification experience.

Unified Push can be self-hosted, or can be used with third-party "distributors". You can learn more about Unified Push and common distributors here:

{% embed url="https://unifiedpush.org/" %}

Once Unified Push is set up, you can configure your BlueBubbles Server with a new Webhook to send notifications/messages to your device.

## Recommended Set Up

Since Unified Push is essentially just a protocol, there can and will be many distributors that support it. You just have to choose one that works best for you. In our recommended setup, we will be using **ntfy.sh** as our provider. It is a free and easy to use service that can be used directly with the BlueBubbles App (v1.15.0+).

1. Ensure you have the BlueBubbles App v1.15.0 or newer installed
2. Install the ntfy.sh app
   1. Google Play: [https://play.google.com/store/apps/details?id=io.heckel.ntfy](https://play.google.com/store/apps/details?id=io.heckel.ntfy)
   2. F-Droid: [https://f-droid.org/en/packages/io.heckel.ntfy/](https://f-droid.org/en/packages/io.heckel.ntfy/)
3. Open the nfty.sh app and allow notification permissions
4. Open the `Settings` page in the BlueBubbles App
5. Navigate to the `Notification Providers` settings page and open the `Unified Push` configuration.
6. Enable Unified Push
   1. Within a few seconds, you should see an `ntfy.sh` URL generated below the toggle
7. Switch to your BlueBubbles Server (Mac)
8. Open the `API & Webhooks` tab
9. Click `Manage` -> `Add New Webhook`
10. Copy/Paste the URL provided in the BlueBubbles App into the new webhook popup
11. Enable the webhook to for `All Events`
12. Save the Webhook

**Now, when you are not actively using the BlueBubbles App, you should still receive notifications via Unified Push + ntfy.sh**
