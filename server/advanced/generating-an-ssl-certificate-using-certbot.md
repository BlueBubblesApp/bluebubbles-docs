---
description: >-
  Learn how you can generate an SSL Certificate using CertBot for BlueBubbles.
  This is especially useful for dynamic DNS setups.
---

# Generating an SSL Certificate using CertBot

{% hint style="info" %}
This guide is for users who are using a dynamic DNS setup, and still want to benefit from the security of using SSL certificates to encrypt traffic over HTTPS.&#x20;
{% endhint %}

## Why would you want to use a SSL certificate with BlueBubbles?

When using a dynamic DNS with BlueBubbles, traffic is not encrypted by default and communication is done over HTTP. In setups such as ones using the Cloudflare or Ngrok proxy services, they get their traffic encrypted via the secure tunnel that is created, and can be accessed using HTTPS.

In order to use a Dynamic DNS setup and have your traffic encrypted, you'll need to setup certificates for the BlueBubbles server to use.

## How to generate SSL certificates

### Installing CertBot

Open Terminal on your Mac and follow these instructions

1. Install CertBot using the instructions on their website, up to and including step 4: [https://certbot.eff.org/instructions?ws=other\&os=osx\&tab=wildcard](https://certbot.eff.org/instructions?ws=other\&os=osx\&tab=wildcard)
2. Go to the following link to see if your dynamic DNS provider is supported by CertBot: [https://eff-certbot.readthedocs.io/en/latest/using.html#dns-plugins](https://eff-certbot.readthedocs.io/en/latest/using.html#dns-plugins)
   * Scroll down to the bottom to find 3rd-party plugins
3. Run the following command to install your corresponding dynamic DNS plugin
   * `$(brew --prefix certbot)/libexec/bin/python -mpip install {{plugin_name}}`
   * Replace `{{plugin_name}}` with the name of your dynamic DNS plugin. For example: `certbot-dns-cloudflare`

### Configuring CertBot

The following will setup a directory in your home folder called `certbot` where all your configs and certificates will be stored.

Run the following commands in Terminal:

1. `mkdir -p ~/certbot/config ~/certbot/work ~/certbot/logs ~/.config/letsencrypt`
   * This will create the 4 required directories
2. `echo "config-dir = ~/certbot/config\nwork-dir = ~/certbot/work\nlogs-dir = ~/certbot/logs\nkey-type = rsa"  > ~/.config/letsencrypt/cli.ini`
   * This will set the directory variables in the Let's Encrypt CLI config file

### Creating your Certificate

You will now create your certificate using the dynamic DNS plugin you identified in step 4 of _Installing CertBot._

Run the following command in Terminal, replacing `{{plugin_flag}}` with your plugin's CLI flag:

`certbot certonly {{plugin_flag}}`

Cloudflare Example Plugin Flag: `--dns-cloudflare`

### Configure auto-renewal of your Certificate

By default, your certificate only lasts a set amount of time. In order to automatically renew your certificate, you will need to configure an app called `cron` on your Mac.

1. Open Terminal and run the following command to open `cron`'s configuration:
   * `crontab -e`
2. Hit the `i` key on your keyboard once
3. Paste the following line into the text area in your Terminal:
   * `0 0,12 * * * /usr/local/bin/python3 -c 'import random; import time; time.sleep(random.random() * 3600)' && /usr/local/bin/certbot renew -q --post-hook "killall BlueBubbles ; sleep 10 && open -a BlueBubbles"`
4. Hit the `Escape` key, then type `:wq` and then hit `Enter`

The `cron` configuration is now saved and your certificate will auto-renew. To check that the renewal process is working, wait at least 12 hours, then check the certbot logs located at `~/certbot/logs/letsencrypt.log`. If you don't see any recent logs, then it's possible the paths in the crontab are not correct. Run the following command and verify the output matches the paths in the crontab entry.
  * `echo "\nPython Path: $(command -v python3) \nCertbot Path: $(command -v certbot) \n"`

### Port-forwarding and Dynamic DNS setup

In order to use a dynamic DNS, you'll need to setup port-forwarding so incoming requests to your router will be forwarded to your Mac.

Follow the guide here on how to do that: [https://docs.bluebubbles.app/server/basic-guides/port-forwarding-and-dynamic-dns](https://docs.bluebubbles.app/server/basic-guides/port-forwarding-and-dynamic-dns)

### Loading your certificates into the BlueBubbles Server

1. Open the BlueBubbles Server
2. Open the Settings tab
3. Set your `Proxy Service` to `Dynamic DNS`
   * Set your Dynamic DNS URL using `https://`
4. Expand the `Advanced Connection Settings` section
5. Enable the `Use Custom Certificate` option
6. Run the following commands, replacing `{{domain}}` with your domain for your Dynamic DNS
   * `rm ~/Library/Application\ Support/bluebubbles-server/Certs/server.key`
   * `rm ~/Library/Application\ Support/bluebubbles-server/Certs/server.pem`
   * `ln -s ~/certbot/config/live/{{domain}}/privkey.pem ~/Library/Application\ Support/bluebubbles-server/Certs/server.key`
   * `ln -s ~/certbot/config/live/{{domain}}/fullchain.pem ~/Library/Application\ Support/bluebubbles-server/Certs/server.pem`
7. Restart the BlueBubbles Server

If it all worked correctly you should be able to now configure your clients to point to your new secure server URL!

{% hint style="info" %}
You can verify that your SSL certificates are setup properly if you can access your server URL using `https://` in your browser. You should see the BlueBubbles Landing Page.
{% endhint %}
