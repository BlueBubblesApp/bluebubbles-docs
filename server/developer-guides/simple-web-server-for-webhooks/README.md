---
description: >-
  This page will show you how you can create a simple HTTP web server to receive
  webhooks from the BlueBubbles Server.
---

# Simple Web Server for Webhooks

## Goal

The goal of this page is to show you how you can do some automation around iMessage using the BlueBubbles Server and the webhook events that it emits.

## Building the HTTP Web Server

Before setting up any webhooks with the BlueBubbles Server, you need to create a web server to receive the webhook requests that the server will be emitting.

There are tools out there built for automation that do this for you such as [n8n](https://n8n.io/). However, if you want full controller over the code, I recommend building a webserver yourself.

Here are some quick implementation code in various languages.

{% embed url="https://docs.bluebubbles.app/server/developer-guides/simple-web-server-for-webhooks/python-web-server" %}

## Creating a Webhook

Once you've built your HTTP server, note the port that the server runs on (if running it on localhost).

Open up the BlueBubbles Server and open the `API & Webhooks` page. Then click on the `Manage` drop down, and select `Add Webhook`

![](<../../.gitbook/assets/CleanShot 2023-03-17 at 10.01.02.png>)

In the popup, enter your webserver's URL + port (if on localhost). Here is an example where my localhost webserver is listening on port 8000:

![](<../../.gitbook/assets/CleanShot 2023-03-17 at 10.02.22.png>)

Next, select the events you want to subscribe to from the `Event Subscriptions` drop down.

Lastly, hit save. You should see your new entry show up in the table.

<figure><img src="../../.gitbook/assets/CleanShot 2023-03-17 at 10.03.30.png" alt=""><figcaption></figcaption></figure>

Assuming your webserver is running, it should now receive HTTP POST requests when the subscribed events are emitted from the server!

{% hint style="info" %}
If you have a cool automation project, please share it with us in our Discord! We love to see all the cool ways people are using BlueBubbles and its' API!
{% endhint %}
