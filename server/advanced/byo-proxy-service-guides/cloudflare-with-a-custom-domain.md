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

1. Login to the Zero Trust dashboard at [https://dash.teams.cloudflare.com/](https://dash.teams.cloudflare.com/) and go to Networks > Tunnels.
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
13. After doing this, you may need to run sudo launchctl start _com.cloudflare.cloudflared_ when initially setting up the tunnel to start it.

As we installed cloudflared as a service, it should automatically launch at startup.

### **Setting the bluebubbles server to the dynamic dns**
1. Navigate to the settings page of the server app
2. Change the proxy to dynamic dns
3. For the URL, type in the url displayed in the tunnels section of the Zero Trust dashboard. Make sure you use HTTPS as cloudflare uses that by default.

Now try opening the bluebubbles app and see if it connects.

### **OPTIONAL : Secure the server using Service Auth on Cloudflare**
1. It is recommended that you ensure the server is fully working and the app is connecting before proceeding.
2. Login to the Zero Trust dashboard at [https://dash.teams.cloudflare.com/](https://dash.teams.cloudflare.com/) and go to Access > Service Auth.
3. Select Create Service Token.
4. Give it a name (This does not have to be the name of your server) like "bb".
5. Set the duration to as long as you want before a new token is required (Non-expiring means you'll not have to reset this in the future).
6. Click Generate Token.
7. Record somewhere the Header and client ID and the Header and client secret. Hit Save.
8. From the left hand menu go to Access > Applications.
9. Select Add an application, and select Self-hosted.
10. Leave everything as default unless specified below.
11. Under Application Name put "Blue Bubbles".
12. Under Subdomain set to the subdomain you used during "Setting up Cloudflare tunnels with your domain" above for example, "bluebubbles"
13. Under Domain select the main domain you used for it. 
14. Click Next.
15. Under Policy name, insert "servicetoken".
16. Set Action to Service Auth.
17. Under Configure Rules, change Selector drop-down to Service Token and select the service token name you set in step 4 above.
18. Click Next & click Add application.
20. In the Blue Bubbles app on Android (or Windows etc), under "Settings/Connection & Server" scroll down to "Configure Custom Headers".
21. Add Header Key "cf-access-client-id" and set Value to your client id (remove "CF-Access-Client-Id:" from the start - ie only insert alphanumericstring.access)
22. Add Header Key "cf-access-client-secret" and set Value to your client secret (remove "CF-Access-Client-Secret:" from the start - ie only insert longalphanumericstring)
23. Hit OK.
24. Test syncing your messages by selecting "Manually Sync Messages" for the last hour.
25. In Cloudflare, from the left hand menu, go to Access > Service Auth and refresh the browser.
26. "Last Seen" should be updated to shown the Service Token has been used.
27. Congrats - your Blue Bubbles server is now secured so only your app can access it.
