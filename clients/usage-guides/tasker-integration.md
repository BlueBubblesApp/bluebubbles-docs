---
description: Details on how BlueBubbles integrates with Tasker
---

# Tasker Integration

{% hint style="warning" %}
Tasker integration requires BlueBubbles App v1.12.0 or greater!
{% endhint %}

{% hint style="info" %}
If you make any cool integrations, feel free to share in our Discord!
{% endhint %}

## Fetching the Server URL

To fetch the server URL in Tasker, follow the below process:

1. Create the `Send Intent` task
   1. Set the `action` as `com.bluebubbles.external.GET_SERVER_URL`
   2. Set one `extra` as `password:<your server password>`. This is required so that apps cannot abuse this ability without your consent.
   3. Set the `package` as `com.bluebubbles.messaging.tanay`.
   4. Ensure the `target` is set as `Broadcast Receiver`.
2. Create the `Intent Received` event
   1. Set the `action` as `net.dinglisch.android.taskerm.BB_SERVER_URL`
   2. Create your own task to perform once this is received. The server URL can be accessed via the `%url` variable.

## Listening for Server Events

To listen for server events (new message, chat read status change, etc), follow the below process:

1. Enable the option within BlueBubbles app settings > Tasker Integration
2. Create the `Intent Received` intent
   1. Set the `action` as `net.dinglisch.android.taskerm.BB_EVENT`
   2. Create your own task to perform once this is received.

The intent sends a few pieces of data:

1. The server URL, which can be accessed via the `%url` variable
2. The type of event, which can be accessed via the `%event` variable
3. The event data, which can be accessed via the `%data` variable

The event type is defined by the server event types, which can be found [here](https://github.com/BlueBubblesApp/bluebubbles-server/blob/master/packages/server/src/server/events.ts).

The event data is a JSON string. You can use Tasker's built-in JSON parser to convert this to a real JSON object and access the individual data inside.



