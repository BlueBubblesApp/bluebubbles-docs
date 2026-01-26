# Use zrok as a Proxy Service

[zrok](https://zrok.io/) is a modern, open-source sharing platform built on [OpenZiti](https://netfoundry.io/docs/openziti/). It's an excellent alternative to Cloudflare or Ngrok for BlueBubbles users who need high bandwidth and stable connections.

## zrok Benefits

- **High bandwidth:** zrok offers a generous 10GB data transfer limit per 24 hours on their free tier.
- **No domain required:** Unlike some Cloudflare setups, zrok provides a public URL automatically.
- **Open source:** zrok is fully open-source, aligning with the BlueBubbles philosophy.

## Setup Instructions

1. Go to [myzrok.io](https://myzrok.io) and sign up for a free account. Follow the email instructions to set your password.

2. Once logged into the zrok dashboard, locate and copy your **Environment Token**. This is the unique key that identifies your server.

3. Configure BlueBubbles server:

    1. Open the BlueBubbles Server app on your macOS device.
    2. Navigate to **Settings > Connection Settings**.
    3. In the **Proxy Service** dropdown menu, select **zrok (Beta)**.
    4. Paste your Environment Token into the provided text field.

4. Restart and connect:

    1. Click **Save** at the bottom of the settings page.
    2. Restart the BlueBubbles Server.
    3. On the server home screen, you should now see a zrok-generated URL. Use this URL (or the QR code) to connect your Android, Windows, or Linux clients.

:::tip
For production-grade environments requiring even more advanced networking, zrok is also available as a managed service called [Frontdoor](https://netfoundry.io/docs/frontdoor/intro).
:::
