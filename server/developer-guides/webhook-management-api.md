---
description: Create, list, and delete BlueBubbles webhooks through the REST API
---

# Webhook Management API

The webhook management endpoints let you configure a server without using the BlueBubbles desktop interface. They are useful for automated and declarative installations.

All examples below use these shell variables:

```bash
export BLUEBUBBLES_URL="https://your-server.example.com"
read -s BLUEBUBBLES_PASSWORD
export BLUEBUBBLES_PASSWORD
BLUEBUBBLES_PASSWORD_QUERY="$(
  printf '%s' "$BLUEBUBBLES_PASSWORD" |
    od -An -tx1 |
    tr -d ' \n' |
    sed 's/../%&/g'
)"
```

Enter the BlueBubbles server password when prompted by `read`. The final command percent-encodes every password byte for safe use in a query string using utilities included with macOS.

{% hint style="warning" %}
Webhook management requests authenticate through a query parameter. Use HTTPS, avoid placing the password directly in scripts or shell history, and take care not to copy authenticated request URLs into logs.
{% endhint %}

The examples use `password`, but the `guid` and `token` query parameter aliases also work. Every endpoint is under `/api/v1/webhook`. The pre-encoded query value avoids curl's newer `--url-query` option, so these commands also work with older curl versions bundled with supported macOS releases.

## List webhooks

Use `GET /api/v1/webhook` to retrieve all configured webhooks:

```bash
curl --silent --show-error \
  "$BLUEBUBBLES_URL/api/v1/webhook?password=$BLUEBUBBLES_PASSWORD_QUERY"
```

A successful response contains an array of webhook records:

```json
{
  "status": 200,
  "message": "Successfully fetched webhooks!",
  "data": [
    {
      "id": 1,
      "url": "https://automation.example.com/bluebubbles",
      "events": [
        "new-message",
        "updated-message"
      ],
      "created": "2026-01-01T00:00:00.000Z"
    }
  ]
}
```

To retrieve one webhook, include its numeric `id`:

```bash
curl --silent --show-error \
  "$BLUEBUBBLES_URL/api/v1/webhook?password=$BLUEBUBBLES_PASSWORD_QUERY&id=1"
```

## Create a webhook

Use `POST /api/v1/webhook` with a JSON body containing:

* `url`: The HTTP or HTTPS endpoint that will receive webhook requests. HTTPS is strongly recommended.
* `events`: An array of webhook event keys. See [REST API & Webhooks](rest-api-and-webhooks.md#webhooks) for the supported events. Use `*` to subscribe to every event.

```bash
curl --silent --show-error \
  --request POST \
  --header "Content-Type: application/json" \
  --data '{
    "url": "https://automation.example.com/bluebubbles",
    "events": ["new-message", "updated-message"]
  }' \
  "$BLUEBUBBLES_URL/api/v1/webhook?password=$BLUEBUBBLES_PASSWORD_QUERY"
```

The response includes the webhook's generated `id`:

```json
{
  "status": 200,
  "message": "Successfully created webhook!",
  "data": {
    "id": 1,
    "url": "https://automation.example.com/bluebubbles",
    "events": [
      "new-message",
      "updated-message"
    ],
    "created": "2026-01-01T00:00:00.000Z"
  }
}
```

Webhook URLs are unique. Posting a URL that is already registered returns the existing webhook without changing its event subscriptions. To replace its subscriptions through the REST API, delete the existing webhook and then create it again.

## Delete a webhook

Use `DELETE /api/v1/webhook/:id`, replacing `:id` with the numeric webhook ID:

```bash
curl --silent --show-error \
  --request DELETE \
  "$BLUEBUBBLES_URL/api/v1/webhook/1?password=$BLUEBUBBLES_PASSWORD_QUERY"
```

A successful deletion returns:

```json
{
  "status": 200,
  "message": "Successfully deleted webhook!"
}
```

Deleting an ID that does not exist returns `404`.

## Troubleshooting

* `400 Bad Request`: Check that `url` starts with `http` and every item in `events` is a supported string event key.
* `401 Unauthorized`: Check that an authentication query parameter is present and matches the server password.
* `404 Not Found`: The webhook ID in a delete request does not exist.
