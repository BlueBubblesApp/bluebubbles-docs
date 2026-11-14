---
description: Documentation for BlueBubbles REST API & Webhooks
---

# REST API & Webhooks

{% hint style="warning" %}
The REST API is currently only compatible with https Ngrok, Cloudflare, or Dynamic DNS connections with valid SSL certificates (when used in a Browser). We hope to add support for LocalTunnel in the near future. If you plan to use the REST API in a non-browser environment, it should work as expected.
{% endhint %}

{% hint style="warning" %}
The REST API requires server version 0.2.0 or greater, though we recommend you use the latest server as some methods in the documentation may not exist on older versions.\
\
Webhooks require server version 1.0.0 or greater.
{% endhint %}

## API Wrappers

* [https://git.elliotnash.org/elliotnash/blueify](https://git.elliotnash.org/elliotnash/blueify) (Java)

## Who's Using It?

{% embed url="https://www.textgpt.chat/" %}
ChatGPT Bot for iMessage (Paid | Closed Source)
{% endembed %}

If you've built something using the API or webhooks that you'd like to share, let us know on our Discord!

## API Documentation

{% embed url="https://documenter.getpostman.com/view/765844/UV5RnfwM" %}
Postman Collection with full documentation
{% endembed %}

## Authentication

Most requests require authentication. Simply add the query parameter `guid` (aliases include `password` & `token`) to the end of the request URL, with the server password as the value.

For example: `https://some-url.ngrok.io/api/v1/ping?guid=password`

## Response Format

JSON API responses use the following envelope. The `data` field is included only when the endpoint returns a value:

```
{
    status: (int) Status Code,
    message: (String) Status Message,
    data?: (any)
}
```

Or in the case of an error:

```
{
    status: (int) Status Code,
    message: (String) Status Message,
    error: {
        type: (String) Error Type,
        message: (String) Error Message
    },
    data?: (any)
}
```

## Webhooks

When a subscribed event occurs, the server sends an HTTP `POST` request to the webhook URL with a `Content-Type` of `application/json`.

Webhook requests use this outer envelope:

```json
{
  "type": "event-name",
  "data": {}
}
```

* `type` identifies the event. The table below lists the keys available for individual subscriptions; wildcard subscriptions can also receive other events emitted by the server.
* `data` is the event-specific payload. It can be an object, array, string, or `null`, and may be omitted for events without a payload. Receivers should branch on `type` before decoding it and tolerate missing `data`.

### Payload examples

A `new-message` event contains a serialized message. The exact fields can vary with the server version, macOS version, message type, and configured payload-size limits, but commonly include:

```json
{
  "type": "new-message",
  "data": {
    "guid": "00000000-0000-0000-0000-000000000000",
    "text": "Hello!",
    "dateCreated": 1720000000000,
    "isFromMe": false,
    "attachments": [],
    "chats": [
      {
        "guid": "iMessage;-;<address>"
      }
    ]
  }
}
```

A typing indicator has a smaller object payload:

```json
{
  "type": "typing-indicator",
  "data": {
    "display": true,
    "guid": "iMessage;-;<address>"
  }
}
```

Some events use a primitive value instead of an object. For example, `new-server` sends the updated server URL:

```json
{
  "type": "new-server",
  "data": "https://example.com"
}
```

### Event keys

The server's [`webhookEventOptions` constant](https://github.com/BlueBubblesApp/bluebubbles-server/blob/development/packages/server/src/server/api/http/constants.ts#L5) is the source of truth for available subscription keys. Compare this table with that constant whenever either list changes.

| Event | Subscription key |
| --- | --- |
| All events | `*` |
| New messages | `new-message` |
| Message updates | `updated-message` |
| Message send errors | `message-send-error` |
| Group name changes | `group-name-change` |
| Group icon changes | `group-icon-changed` |
| Group icon removal | `group-icon-removed` |
| Participant removed | `participant-removed` |
| Participant added | `participant-added` |
| Participant left | `participant-left` |
| Chat read status changes | `chat-read-status-changed` |
| Typing indicators | `typing-indicator` |
| Scheduled message errors | `scheduled-message-error` |
| Server updates | `server-update` |
| New server URL | `new-server` |
| Find My location updates | `new-findmy-location` |
| WebSocket hello world | `hello-world` |
| Incoming FaceTime call | `incoming-facetime` |
| FaceTime call status changes (experimental) | `ft-call-status-changed` |
| iMessage alias removed | `imessage-alias-removed` |
| Theme backup created | `theme-backup-created` |
| Theme backup updated | `theme-backup-updated` |
| Theme backup deleted | `theme-backup-deleted` |
| Settings backup created | `settings-backup-created` |
| Settings backup updated | `settings-backup-updated` |
| Settings backup deleted | `settings-backup-deleted` |

Subscribe to `*` to receive every event dispatched by the server, including events that are not available as individual subscription keys. For example, scheduled-message creation, update, deletion, and sent events are delivered to wildcard subscribers; `scheduled-message-deleted` has an array payload, and `scheduled-message-updated` can omit `data`. Wildcard receivers should tolerate unknown event types. Otherwise, select only the event keys your receiver handles.

The current server [emits alias removals](https://github.com/BlueBubblesApp/bluebubbles-server/blob/development/packages/server/src/server/events.ts) as `imessage-aliases-removed`, which differs from the selectable `imessage-alias-removed` key. To receive those events, subscribe to `*` and handle the emitted plural form.

Webhook delivery is best effort. The server does not retry a failed request, so the receiver should respond with a successful status promptly and queue longer-running work for later processing.
