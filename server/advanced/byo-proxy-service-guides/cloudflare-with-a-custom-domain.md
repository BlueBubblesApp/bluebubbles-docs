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

1. Login to the Zero Trust dashboard at [https://dash.teams.cloudflare.com/](https://dash.teams.cloudflare.com/) and go to Access > Tunnels.
2. Select Create a tunnel
3. Enter a name for your tunnel. For example, you could name it Bluebubbles.
4. Select Save tunnel
5. Next, you will need to install cloudflared and run it. You can install it here, [https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation/](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation/)
6. After installing cloudflared, you can see that when choosing your OS as Mac, cloudflare provides a command to enter into terminal. Run this command.
7. Once the command has been run successfully, your connector will show up underneath the command in the Zero Trust Dashboard.
8. Select Next
9. Now in the Public Hostnames tab, type in your subdomain, for example, bluebubbles.(This does not have to be the name of your tunnel)
10. Choose the main domain you want to use for it.
11. Below, you should see a section called Service. For this, you want to put the localhost address for the bluebubbles server. The default one would be: HTTP://localhost:1234 .
12. Save the tunnel

As we installed cloudflared as a service, it should automatically launch at startup.

### **Setting the bluebubbles server to the dynamic dns**
1. Navigate to the settings page of the server app
2. Change the proxy to dynamic dns
3. For the URL, type in the url displayed in the tunnels section of the Zero Trust dashboard. Make sure you use HTTPS as cloudflare uses that by default.

Now try opening the bluebubbles app and see if it connects.
