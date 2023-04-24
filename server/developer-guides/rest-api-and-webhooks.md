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

We support listening to the following events:

1. New Messages
2. Message Updates (delivered, read, etc)
3. Message Errors
4. Group Name Changes
5. Participant Added / Removed / Left
6. Chat Read Status Changes
7. Typing Indicators
8. BB Server Update
9. BB Server URL Change
10. Hello World (for testing)

You can also subscribe to an event that listens to all of the above at once.

Simply provide a URL and the server will POST to it whenever your desired event occurs.
