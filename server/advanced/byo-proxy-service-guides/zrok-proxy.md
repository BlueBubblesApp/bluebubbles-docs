# Use zrok as a Proxy Service

[zrok](https://zrok.io/) is a modern, open-source sharing platform built on [OpenZiti](https://netfoundry.io/docs/openziti/). It's an alternative to Cloudflare or Ngrok for BlueBubbles users who need high bandwidth and stable connections.

## zrok benefits

* **High bandwidth**: zrok offers a 5GB data transfer limit per 24 hours on their free tier, with pricing options to purchase more bandwidth if needed.
* **No domain required**: Automatically provides a public URL.
* **Open source and self-hostable**: zrok is fully [open-source on GitHub](https://github.com/openziti/zrok). You can use the managed service at zrok.io or [host it yourself](https://netfoundry.io/docs/zrok/guides/self-hosting/linux/) for total data sovereignty and unlimited bandwidth.
* **Zero-trust security**: Unlike traditional proxies, zrok is built on OpenZiti, providing a secure, outbound-only connection without opening firewall ports.
* **Built-in**: The zrok client is already integrated directly into the BlueBubbles Server.

## Setup Instructions

1. Go to [myzrok.io](https://myzrok.io) and sign up for a free account. Follow the email instructions to set your password.

2. Once logged into the zrok dashboard, locate and copy your **Account Token**. This is the unique key that identifies your server.

3. Configure BlueBubbles server:

    1. Open the BlueBubbles Server app on your macOS device.
    2. Navigate to **Settings > Connection Settings**.
    3. In the **Proxy Service** dropdown menu, select **zrok (Beta)**.
    4. Paste your account token into the provided text field.

4. Restart and connect:

    1. Click **Save** at the bottom of the settings page.
    2. Restart the BlueBubbles Server.
    3. On the server home screen, you should now see a zrok-generated URL. Use this URL (or the QR code) to connect your Android, Windows, or Linux clients.

:::tip
For production-grade environments requiring even more advanced networking, zrok is also available as a managed service called [Frontdoor](https://netfoundry.io/docs/frontdoor/intro).
:::
