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

All requests are returned with the basic format as below:

```
{
    status: (int) Status Code,
    message: (String) Status Message,
    data: (Variable)
}
```

Or in the case of an error:

```
{
    status: (int) Status Code,
    message: (String) Status Message,
    error: {
        type: (String) Error Type,
        error: (String) Error Message
    },
    data?: (any)
}
```

## Webhooks

When a subscribed event occurs, the server sends an HTTP `POST` request to the webhook URL with a `Content-Type` of `application/json`.

Every webhook uses the same outer envelope:

```json
{
  "type": "event-name",
  "data": {}
}
```

* `type` is one of the event keys listed below.
* `data` is the event-specific payload. It can be an object, string, or `null`, so receivers should branch on `type` before decoding it.

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

Subscribe to `*` to receive every supported event. Otherwise, select only the event keys your receiver handles.

Webhook delivery is best effort. The server does not retry a failed request, so the receiver should respond with a successful status promptly and queue longer-running work for later processing.
