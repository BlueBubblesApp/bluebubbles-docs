# Use zrok as a Proxy Service

[zrok](https://zrok.io/) is a modern, open-source sharing platform built on [OpenZiti](https://netfoundry.io/docs/openziti/). It's an alternative to Cloudflare or Ngrok for BlueBubbles users who need high bandwidth and stable connections.

## zrok Benefits

* **High bandwidth**: zrok offers a 5GB data transfer limit per 24 hours on their free tier, with pricing options to purchase more bandwidth if needed.
* **No domain required**: Automatically provides a public URL.
* **Open source and self-hostable**: zrok is fully [open-source on GitHub](https://github.com/openziti/zrok). You can use the managed service at zrok.io or [host it yourself](https://netfoundry.io/docs/zrok/guides/self-hosting/linux/) for total data sovereignty and unlimited bandwidth.
* **Zero-trust security**: Unlike traditional proxies, zrok is built on OpenZiti, providing a secure, outbound-only connection without opening firewall ports.
* **Built-in**: The zrok client is already integrated directly into the BlueBubbles Server.

## Setup Instructions

1. Go to [myzrok.io](https://myzrok.io) and sign up for a free account. Follow the email instructions to set your password.
2. Log into the zrok dashboard and copy your **Account Token** from the portal.

### Option A: Configuration via the Setup Wizard (New Install)

Firebase is required to sync your tunnel URL with your mobile devices and to deliver real-time notifications.

#### Step 1: Firebase Configuration

1. Create a project in the [Firebase Console](https://console.firebase.google.com/).
2. Register an **Android** app using the package name `com.bluebubbles.messaging`.
3. Download the `google-services.json` file from the **General** tab in your Firebase Project Settings.
4. Go to the **Service accounts** tab and click **Generate new private key** to download your service account JSON file.
5. Drag both JSON files into the BlueBubbles setup wizard and click the **floppy disk save icon** for each field.
6. Click **Next** to proceed to the **Connection** slide.

#### Step 2: zrok Configuration

1. Enter a **Server Password** and click the **floppy disk save icon**.
2. In the **Proxy Setup** dropdown, select **zrok**.
3. Paste your **Account Token** into the token field and click the **floppy disk save icon**.
4. Confirm that a `*.share.zrok.io` URL appears, then click **Next** and proceed through the wizard.

### Option B: Configuration via Settings (Existing Install)

If you've already completed the initial setup and configured Firebase, you can switch to zrok in the settings:

1. Open the BlueBubbles Server app and navigate to **Settings > Connection Settings**.
2. Change the **Proxy Service** dropdown to **zrok**.
3. Paste your **Account Token** into the text field.
4. Click the **floppy disk save icon** next to the token.
5. Click **Save** at the bottom of the page and **Restart** the server when prompted.

## Connecting your Clients

1. Find the public URL (e.g., `https://*.share.zrok.io`) at the top of the server home screen once you've finished the setup.
2. Use this URL or the displayed QR code to link your Android, Windows, or Linux clients to the server.
3. Log into your [zrok portal](https://myzrok.io) to verify the connection is active.
4. Look for your device in the portal's explorer view; a successful connection will show an active frontend mapping to your server's local port.

:::tip
For production-grade environments requiring even more advanced networking, zrok is also available as a managed service called [Frontdoor](https://netfoundry.io/docs/frontdoor/intro).
:::
