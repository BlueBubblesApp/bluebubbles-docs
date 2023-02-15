---
description: >-
  This guide will show you how to port-forward using your home's router, so you
  can connect directly to your BlueBubbles server using a Dynamic DNS. No need
  for a third-party proxy service.
---

# Port Forwarding & Dynamic DNS

Before you begin, make sure that the following pre-requisites apply to you. If _**any**_ of them do not apply to you, this will not be possible for you, and you may need to stick to the build-in proxy services, pay for one, or self-host a reverse proxy.

## Pre-requisites

* You have access to your network's router
  * This doesn't need to be physical access
  * You will need to know how to login to your router's UI via your browser. This guide will walk you through part of it but will not provide you with the required authentication information. You must already know this.
  * This will not work on school or work networks
* Your macOS device must already be setup (physical or VM)
* Your macOS device must have a static IP Address.
  * If you don't already have a static IP, learn how to set one [here](https://www.youtube.com/watch?v=rzyBJ0dnjBE)

## Before You Begin

Read these notes before you begin, just in case...

* If you are running macOS in a VM, make sure the network adapter is configured as "Bridged".
  * This means that the VM will connect directly to your router, rather than through the host computer.
  * This allows external connections to connect directly to your mac server.
* By default, this setup utilizes HTTP, **not** HTTPS. In order to use HTTPS, you will need to know how to setup a certificate with your dynamic DNS provider. Whether that be via your router, or manual using Let's Encrypt.
  * This implies that your data is unencrypted when no certificate is implemented.
  * You can utilize the `Encrypt Messages` option in the BlueBubbles server settings to protect some of your data, but not all.
* If no certificate is implemented, and you are connecting over HTTP, BlueBubbles Web will not be usable. This is because browsers now require a secure connection when making API calls to a server.

## Port Forwarding Guide

Now that you've read the pre-requisites and the "before you begin" notes, you can start the setup.

1. Get the IP of both your router and your macOS device (or VM).
   1. To get this information, open `System Preferences` and find the `Network` settings.
   2. Click on the connected `Ethernet` or `WiFi` device on the left sidebar.
   3. On the right-hand side, find the `IP Address` entry, and under that, the `Router` IP entry
      1. If you _do not_ see `IP Address` or `Router` entries on the right side, click `Advanced`, and then select the `TCP/IP` tab.
   4. Copy those IPs somewhere you can reference.
2. Open your browser and navigate to your router's home page
   1. In your browser's URL bar, enter `http://<router IP>`, replacing `<router IP>` with the router entry you copied earlier. If your router's home page connects over HTTPS, use `https://` instead. However, the majority of routers utilize HTTP by default.
   2. Your router's IP will typically look something like one of the following:
      1. `192.168.0.1`
      2. `10.0.0.1`
      3. `192.168.50.1`
   3. If you get one of those `Your connection isn't private` browser pages, simply type `thisisunsafe` directly into the browser window (not the URL bar) and the site should load.
3. When prompted to enter your login credentials, enter your username and password to login.
   1. It's typical that the router has the default username and password printed on a sticker, on the device itself.
   2. If you do not know your username or password, it's fairly typical for the default username to be `admin`. However, the password can vary by manufacturer. A list of possible default credentials can be found [here](https://www.softwaretestinghelp.com/default-router-username-and-password-list/).
4. Next, you will need to find the Port-forwarding settings. Many routers will list it in the sidebar under `Port Forwarding`, or similar. However, some routers may hide those settings under the `WAN` tab in the sidebar.&#x20;
   1. If you cannot find the port-forwarding configuration page, you might have luck finding it after reading one of the guides [here](https://portforward.com/router.htm)
5. Once on the port forwarding configuration page, click the button to add a new port forwarding configuration/profile.
6. You will be prompted to enter some information:
   1. **Name:** You can enter any name
   2. **Protocol:** TCP or Both
   3. **External Port:** This is the port that you will use in your server URL to connect.
      1. This can be the same as the port that BlueBubbles is running on.
   4. **Internal IP Address:** The IP of your macOS device that you copied earlier
   5. **Internal Port:** The port that the BlueBubbles server is configured with
7. Save the configuration/profile
8. Read the next section to figure out how to verify that everything was setup properly

## Port-Forwarding Verification

This section will show you how to verify that you've port-forwarded correctly.

1. On your mac, open your browser and navigate to [https://canyouseeme.org/](https://canyouseeme.org/)
2. In the `Port` field, enter in the `External Port` you configured when setting up the port-forwarding.
3. Click the `Check Port` button
   1. You will receive a `Success` message if you've configured port-forwarding correctly.
   2. You will see an `Error` if you did not configure port-forwarding correctly.

If your verification failed, it means something went wrong in the process. Here are some things you can try to possibly fix any issues:

* Go through the guide again and make sure that everything is configured properly
* If you _did not_ setup a static IP for your mac, your mac's IP Address may have changed, and you need to configure your port-forwarding profile with the new IP Address.
* Maybe you entered the BlueBubbles port into the CanYouSeeMe website, and not the External Port you configured within your router.
* Run the BlueBubbles server and try again.

## Dynamic DNS Setup

Once you have setup port-forwarding, the next step would be to sign up and use a Dynamic DNS service to connect your home IP Address to a domain. Here are some free Dynamic DNS providers that are fairly popular and well-liked:

* [No-IP](https://www.noip.com/)
  * No-IP now includes 1 free certificate, which you can apply [here](https://my.noip.com/my-services/ssl-certificates)
* [DynDNS](https://dyn.com)
* [DuckDNS](https://www.duckdns.org/)

Once you have one picked, sign up for a free account. And setup a new Dynamic DNS configuration.

Next, you will need to download the corresponding "DUC" (Dynamic Update Client) for your provider. This allows your mac to update the DNS record whenever it's router's external IP changes. Without it, you will lose connection to your server whenever your external IP changes.

Here are the DUC clients for the providers above:

* [No-IP DUC](https://www.noip.com/download)
* [DynDNS DUC](https://help.dyn.com/updater-mac/)
* [DuckDNS DUC](https://www.duckdns.org/install.jsp)

Once the DUC is installed, login or configure it with your account.

Lastly, add the DUC app to your mac account's login items so it runs each time the computer is rebooted.

You will now be able to connect to your BlueBubbles server via your Dynamic DNS domain + Port Forwarded Port. For example: `http://<dynamic dns>:<port>`

## Configuration in BlueBubbles

In order to use your new Dynamic DNS with BlueBubbles, simply follow these steps.

1. Open your BlueBubbles server
2. Open the Settings tab
3. Find the `Proxy Service` drop down and select `Dynamic DNS`.
4. You will get a popup window asking you for a server URL. Enter your dynamic DNS URL + Port (i.e. `http://<dynamic dns>:<port>`)
5. If you have an SSL Certificate, set your server URL to HTTPS instead of HTTP. Make sure to also enable `Settings > Advanced Connection Settings > Use Custom Certificate`. Once enabled, a self-signed certificate (`server.key` and `server.pem`) will be created in `~/Library/Application Support/bluebubbles-server/Certs`. Replace both files with your certificate files. It is important to name these files exactly the same as the self-signed certificate files that were created. _Note: If your certificate file ends in .crt, simply rename it to .pem._
6. Restart your BlueBubbles server to activate your new settings.
7. You should now be configured to use BlueBubbles via port-forwarding.
