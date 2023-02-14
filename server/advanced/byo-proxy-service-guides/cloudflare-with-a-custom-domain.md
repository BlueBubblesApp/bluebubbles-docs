# Cloudflare with a Custom Domain

Pros:

* Static URL, so no need to update Dynamic DNS in BlueBubbles server
* Auto start at boot
* You can configure a custom landing page to block unwanted connections to your server (in addition to the default one in BlueBubbles)

Cons:

* You need a domain to link to Cloudflare (subdomain services like DuckDNS and No-IP won't work)
* Setup is a tad bit more complicated

## Installation

### **Linking a domain to Cloudflare**

1. Sign up for a Cloudflare account at [https://dash.cloudflare.com/sign-up](https://dash.cloudflare.com/sign-up)
2. Add a site on the portal&#x20;
3. Enter your domain name (do not use a subdomain)&#x20;
4. Click the free plan and click continue&#x20;
5. If you are using the domain for any other websites copy the records below (if you are just using the domain for BlueBubbles you can skip this part)
6. Configure your domain name servers to Cloudflare
7. Wait for Cloudflare to validate your domain

### **Setting up Cloudflare tunnels with your domain**

1. In the terminal type `cloudflared tunnel create <NAME>` - replace name to the desired name of your tunnel, it can be anything
2. Download the following config.yml template open it text edit (or other text editors) and replace the \[username] with the username of your mac server and replace the \[tunnel id] with your tunnel id (if your forgot it in the terminal type `cloudflared tunnel list` and it should show up&#x20;
   * [https://raw.githubusercontent.com/Rihcus/Bluebububbles-Cloudflare-guide/main/config.yml](https://raw.githubusercontent.com/Rihcus/Bluebububbles-Cloudflare-guide/main/config.yml)
3. move the config.yml file to `/Users/[username]/.cloudflared`
4. in the terminal type `cloudflared run <NAME>`
5. in BlueBubbles settings set the proxy service to dynamic dns and enter your hostname and port
6. Try to connect the server using the Android, Desktop, or Web App to see if it works

### **Setting Cloudflare to launch at boot**

1. in the terminal type `sudo cloudflared service install`
2. download the `com.cloudflare.cloudflared.plist` template and replace \[YOUR TUNNEL ID] to your cloudflare tunnel id without the brackets (if your forgot it in the terminal type `cloudflared tunnel list` and it should show up) [https://raw.githubusercontent.com/Rihcus/Bluebububbles-Cloudflare-guide/main/com.cloudflare.cloudflared.plist](https://raw.githubusercontent.com/Rihcus/Bluebububbles-Cloudflare-guide/main/com.cloudflare.cloudflared.plist)
3. Copy your modified `com.cloudflare.cloudflared.plist` to `/Library/LaunchDaemons` (if prompted to overwrite click yes)
4. type in terminal `sudo launchctl start com.cloudflare.cloudflared` to start the service, cloudflare should auto start at boot now

#### **Helpful resources:**

[https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation)

[https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/create-tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/create-tunnel)

[https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/configuration/configuration-file](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/configuration/configuration-file)

[https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/run-tunnel/run-as-service](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/run-tunnel/run-as-service)

[https://support.apple.com/lv-lv/guide/terminal/apdc6c1077b-5d5d-4d35-9c19-60f2397b2369/mac](https://support.apple.com/lv-lv/guide/terminal/apdc6c1077b-5d5d-4d35-9c19-60f2397b2369/mac)
