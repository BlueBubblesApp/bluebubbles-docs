---
description: >-
  If you want to perform a manual setup of Nginx, instead of using the Nginx
  Proxy Manager, here is a guide on how to do it. Big thanks to @JustBen in the
  BlueBubbles Discord, for putting this guide tog
---

# Nginx Manual Setup

This page will guide you on setting up your own reverse proxy, given that you own a server to use for it. In order for this setup to work, you will need control of a server. This is typically a Linux server but could theoretically be any operating system. **This guide will be for setting up a reverse proxy on Linux specifically.** This will also assume that you have a Linux server on the same local network as your BlueBubbles server, as having the proxy and server on different networks adds several layers of complexity.

### Overview

While the built-in proxy services in BlueBubbles are simple and easy to use, many users want a completely self-hosted setup where they control all pieces of the system. More specifically, control over the server, client, proxy, and domain. This eliminates almost all dependence on third-party resources and services.

The best use-case for this is that you already run a personal server of some sort and maybe you're already running a proxy for services running on this personal server. There is arguably more privacy in owning and controlling the proxy that handles your BlueBubbles traffic, or maybe you just enjoy learning about and/or managing application and server tools.

That said, the built-in options to BlueBubbles are more well-suited for the majority of users as they are generally reliable, secure, and (most importantly) easy to configure.

#### Pros & Cons

| Pros                                                                      | Cons                               |
| ------------------------------------------------------------------------- | ---------------------------------- |
| Completely self-hosted, no need to use a third-party service              | Server maintenance & upkeep        |
| More secure, entirely controlled and operated by yourself                 | More prerequisites, niche use case |
| More control, allowing you to configure everything exactly as you want it | More complex configuration         |

### Pre-requisites

* A server at your disposal on the same local network (LAN) as your BlueBubbles server
* A static IP or DHCP reservation configured for your BlueBubbles server on your LAN
* A static or reliable way to reach your home WAN IP (publicly addressable IP). This could be:
  * A static IP on your router. This is unlikely, as your WAN IP is usually not guaranteed by your ISP to stay the same, and it may change unexpectedly
  * A domain name and Dynamic DNS configured (more information below)
* Port forwarding of your public BlueBubbles ports (recommended 80 and 443) to your proxy server
  * DISCLAIMER: If you aren't able to or don't know how to configure port forwarding, it is HIGHLY recommended that you instead opt for the built-in BlueBubbles proxy services
* (Optional) A domain name
  * If you have a domain name, we recommend configuring Dynamic DNS. This will ensure that your domain name will always point to your WAN IP (which may change unexpectedly and/or frequently). Your registrar/DNS server must support Dynamic DNS, and configuration will depend on your router and your registrar/DNS. For a starting point, you can view the [DD-WRT wiki page on Dynamic DNS](https://wiki.dd-wrt.com/wiki/index.php/Dynamic\_DNS).

### Variables

Of course, there are certain things that will vary depending on your configuration and setup. The following variables will be used as placeholders in the example configurations below. They will appear in the example configurations exactly as they appear here.

| Variable name       | Description                                                                                                                                                 | Example values                                                           |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| \<server name>      | Domain name or WAN IP of your server to which the proxy will be responding                                                                                  | <p><code>bluebubbles.example.com</code><br><code>150.50.31.10</code></p> |
| \<BlueBubbles IP>   | The IP of your BlueBubbles server on the local network. If the proxy is running on the same server as your BlueBubbles server (as in a VM), use `localhost` | <p><code>192.168.0.10</code><br><code>localhost</code></p>               |
| \<BlueBubbles port> | The port that the BlueBubbles server is listening at on the local network (default is 1234)                                                                 | `1234`                                                                   |

### Nginx

The following is the recommended configuration for BlueBubbles to work behind an Nginx proxy. For easiest configuration, this should be placed in a new file at `/etc/nginx/conf.d/bluebubbles.conf`

```
    server {
        listen			    80;
        server_name		    <server name>;
        proxy_set_header    Host $host;
        proxy_http_version  1.1;
        proxy_set_header 	Connection "Upgrade";
        proxy_set_header 	Upgrade $http_upgrade;
        client_max_body_size 512M;
    
        location / {
            proxy_pass	        http://<BlueBubbles IP>:<BlueBubbles port>;
        }
    }
```

After creating this file and filling in the appropriate values, restart the Nginx service with `systemctl restart nginx.service` (you may need `sudo`).

#### SSL

If you plan to use SSL (recommended), see the SSL Configuration section at the bottom of this guide.

#### Other considerations

* The directory `conf.d` may be named something else like `vhosts.d`, depending on your Linux distribution
* You can change `client_max_body_size` to whatever you want. You can use `M` and `G` to denote Megabytes and Gigabytes respectively, or you can set this to 0 (with no units) to put no restriction on the max client body size. This changes the max file size that can be sent through the proxy to BlueBubbles. For more information, you can see the [nginx documentation](http://nginx.org/en/docs/http/ngx\_http\_core\_module.html#client\_max\_body\_size)
* If you have further problems with large files not sending to BlueBubbles, you can also define the configured `client_body_buffer_size` similar to `client_max_body_size`

### Apache

Before writing your config, these apache modules are required for the config to work:

* `mod_proxy`
* `proxy_http`
* `proxy_wstunnel`
* `mod_headers`

The following is the recommended configuration for BlueBubbles to work behind an Apache proxy. This should be placed in a new file at `/etc/httpd/conf.d/bluebubbles.conf`

```
    <VirtualHost *:80>
        ServerName <server name>

        ProxyPreserveHost On
        RequestHeader set X-ForwardedPort "80"
        RequestHeader set X-Forwarded-Proto "http"

        ErrorLog <error log location>
        CustomLog <access log location> common

        <Location />
            ProxyPass http://<BlueBubbles IP>:<BlueBubbles port>/
            ProxyPassReverse http://<BlueBubbles IP>:<BlueBubbles port>/
            ProxyPass ws://<BlueBubbles IP>:<BlueBubbles port>/
            ProxyPassreverse ws://<BlueBubbles IP>:<BlueBubbles port>/
        </Location>
    </VirtualHost>
```

After creating this file and filling in the appropriate values, restart the apache service with `systemctl restart httpd` (you may need `sudo`)

#### SSL

If you plan to use SSL (recommended), see the SSL Configuration section at the bottom of this guide.

#### Other considerations

On certain Linux distributions, the configuration directory may be at `/etc/apache2/vhosts.d/` or `/etc/httpd/vhosts.d/`

For reference, the following is the minimum viable configuration for BlueBubbles to work behind an Apache proxy. This configuration does not need the `mod_headers` module.

```
    <VirtualHost *:80>
        ServerName {server name}
    
        <Location />
            ProxyPass http://<BlueBubbles IP>:<BlueBubbles port>/
            ProxyPass ws://<BlueBubbles IP>:<BlueBubbles port>/
        </Location>
    </VirtualHost>
```

### HAProxy

The following is the recommended configuration template for HAProxy. This should be in `/etc/haproxy/haproxy.cfg`.

```
    frontend
    	use_backend <backend name> [if {ACL}]
    
    backend <backend name>
    	timeout client          60m
    	timeout http-keep-alive 5m
    	server <be server name> <BlueBubbles IP>:<BlueBubbles port>
```

Keep in mind that HAProxy ACL configurations can be complex. For more information on configuring HAProxy ACLs, see [this HAProxy blog post](https://www.haproxy.com/blog/introduction-to-haproxy-acls/).

There are a few variables specific to HAProxy here:

| Variable name     | Description                                                                                                                             | Example values                                                       |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| \<backend name>   | HAProxy backend name. This is an arbitrary name used to identify services in your HAProxy configuration file(s)                         | <p><code>bluebubbles</code><br><code>imessage-relay</code></p>       |
| \<be server name> | HAProxy backend server name. This is an arbitrary name used to identify a specific backend server in your HAProxy configuration file(s) | <p><code>bb-server</code><br><code>imessage-relay-primary</code></p> |

After writing/changing these files, reload HAProxy with `systemctl restart haproxy` (you may need `sudo`)

#### SSL

If you plan to use SSL (recommended), see the SSL Configuration section at the bottom of this guide.

### SSL configuration

If you do not use SSL/HTTPS (used interchangeably here), traffic sent from your client to your server will be sent in plain text. This includes message content and your BlueBubbles password. As such, configuring SSL is highly recommended if you intend to connect to your BlueBubbles server over the internet.

To access your BlueBubbles to use HTTPS, you need to use an SSL certificate and configure your proxy to either terminate or pass through the SSL traffic. Here we will provide example configurations that have SSL certificates configured and link to more resources for configuring SSL on your proxy.

Keep in mind that **you can only use SSL if you have a domain** -- it will not work with only an IP address.

#### What is SSL termination?

The easiest way to use this is to use something called "SSL termination" or "SSL offloading." This just means that the connection from client to server uses HTTPS (encrypted traffic) to the proxy, then the proxy communicates with the backend server using HTTP (unencrypted/plaintext traffic). As long as the proxy server and backend server live on the same private network, there are minimal security concerns with this setup, as the unencrypted traffic is sent over a trusted, private network.

The primary alternative to SSL termination is SSL passthrough, in which the traffic is not decrypted at the proxy, but instead is passed through to the server where the traffic is decrypted. There are other alternatives such as two-way SSL in which the client talks to the proxy on one SSL connection, then the proxy opens a different SSL connection to talk with the backend server over an encrypted channel.

For simplicity's sake, we will only be covering SSL offloading configuration in this guide.

#### Certbot, the easy solution

The simplest solution for SSL certificate management and configuration is [Certbot](https://certbot.eff.org/). Cerbot is a tool built by the Electronic Frontier Foundation (EFF) and leverages [Let's Encrypt](https://letsencrypt.org/) to issue free certificates and then automatically install them in your existing server/proxy configuration. Certbot supports automatic SSL configuration for Apache, Nginx, HAProxy, and Plesk.

If you are using HAProxy, there are some added layers of complexity, but Certbot can still be used to seamlessly automate the management and renewal of SSL certificates. Skip to the section _Certbot on HAProxy_.

This is highly recommended as the solution for configuring SSL/HTTPS. This solution is easy, robust, and automated. Unless you have a good reason not to use Certbot, we highly recommend this option.

**Certbot configuration steps**

If you are using HAProxy, skip to the section _Certbot on HAProxy_.

On almost all Linux systems, the configuration is simple:

1. Install certbot with your distribution's package manager
   * You may also need to install the certbot addon for your specific proxy, e.g. `certbot-nginx` (package names vary by distro)
2. Configure your service as you normally would, with HTTP-only
   * You need to configure your service before running Certbot to configure SSL on your service. This is because Certbot will look through your config to match the `<server name>` you provide it with the server name in your configuration file. It does this to identify where it needs to add the configuration for SSL.
3. Issue a new certificate:
   * Run: `sudo certbot --standalone --<proxy> -d <server name>`
     * Fill in `<proxy>` appropriately for your proxy application (`apache`, `nginx`)
   * If this is your first time running certbot, it will run you through a simple first-time setup that just requires your email address, confirmation of the Terms of Service, and whether or not you want to receive emails about EFF projects and campaigns
4. Use cron to automatically renew the certificate(s) every 2 months
   * Add a line to your crontab
     * `sudo crontab -e`
     * Insert on its own line: `0 0 1 */2 * certbot renew`
   * Enable the cron service, if it isn't already:
     * `sudo systemctl enable --now cron`

**Certbot on HAProxy**

Per Certbot's website: "Certificate installation with HAProxy is complicated." There are a few things that makes Certbot with HAProxy somewhat complicated:

* HAProxy is not a web server, so it can not be used in `standalone` mode to run an HTTP server and respond to Let's Encrypt's HTTP-01 challenges
* HAProxy requires a single .pem file that includes both the public certificate and the private key. This is not automatically generated by Certbot.

However, there are several guides available online which show you how to generate a certificate, install it, and then automate the renewall process. In short, it works by diverting HTTP calls to the `.well-known` directory (where HTTP-01 challenges are stored) to the localhost where Certbot is running its own web server during the challenge. Then, the new certificate and key are combined into a .pem file with a simple bash script.

* [Servers for Hackers - LetsEncrypt with HAProxy](https://serversforhackers.com/c/letsencrypt-with-haproxy) (Recommended)
* [Skarlso - How to HTTPS with Hugo LetsEncrypt and HAProxy](https://skarlso.github.io/2017/02/15/how-to-https-with-hugo-letsencrypt-haproxy/)
* [DigitalOcean - How To Secure HAProxy with Let's Encrypt on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-secure-haproxy-with-let-s-encrypt-on-ubuntu-14-04)

_An example configuration file may be provided in the future after more testing can be done._

#### Self-configured SSL

To reiterate: **Certbot is highly recommended as an easy solution for SSL configuration and management**. If you do intend to configure SSL yourself, we have provided example configurations below and references to further reading on the subject. If you're having trouble, feel free to join our Discord and ask questions there (linked in the header above).

These example configuration files are the auto-generated files by Certbot and are provided for reference.

**Nginx**

[Nginx documentation SSL termination](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/)

Contents of `/etc/nginx/conf.d/bluebubbles.conf` (could also be `/etc/nginx/vhosts.d/bluebubbles.conf`, depending on distro)

```
    server {
        listen               443 ssl;
        server_name          <server name>;
        proxy_set_header     Connection "Upgrade";
        proxy_set_header     Upgrade $http_upgrade;
        proxy_http_version   1.1;
        client_max_body_size 512M;

        location / {
            proxy_pass    http://<BlueBubbles IP>:<BlueBubbles port>;
        }
           
        ssl_certificate /etc/letsencrypt/live/<server name>/fullchain.pem
        ssl_certificate_key /etc/letsencrypt/live/<server name>/privkey.pem
        include /etc/letsencrypt/options-ssl-nginx.conf
        ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem
    }
    
    server {
        listen              80;
        server_name         <server name>;
        
        # HTTP to HTTPS redirect
        if ($host = <server name>) {
            return 301 https://$host$request_uri;
        }
        
        return 404;
    }
```

Contents of `/etc/letsencrypt/options-ssl-nginx.conf`

```
ssl_session_cache shared:le_nginx_SSL:10m;
ssl_session_timeout 1440m;
ssl_session_tickets off;

ssl_protocols TLSv1.2 TLSv1.3;
ssl_prefer_server_ciphers off;
```

**Apache**

[Apache documentation on mod\_ssl](https://httpd.apache.org/docs/2.4/mod/mod\_ssl.html)

Contents of `/etc/httpd/conf.d/bluebubbles.conf` (could also be `/etc/apache2/vhosts.d/bluebubbles.conf`, depending on Linux distribution)

```
    <VirtualHost *:443>
        ServerName <server name>

        ProxyPreserveHost On
        RequestHeader set X-ForwardedPort "443"
        RequestHeader set X-Forwarded-Proto "https"

        ErrorLog <error log location>
        CustomLog <access log location> common

        <Location />
            ProxyPass http://<BlueBubbles IP>:<BlueBubbles port>/
            ProxyPassReverse http://<BlueBubbles IP>:<BlueBubbles port>/
            ProxyPass ws://<BlueBubbles IP>:<BlueBubbles port>/
            ProxyPassreverse ws://<BlueBubbles IP>:<BlueBubbles port>/
        </Location>
        
        SSLEngine on
        SSLCertificateFile /etc/letsencrypt/live/<server name>/cert.pem
        SSLCertificateKeyFile /etc/letsencrypt/live/<server name>/privkey.pem
        SSLCertificateChainFile /etc/letsencrypt/live/<server name>/fullchain.pem

    </VirtualHost>
    
    # HTTP to HTTPS redirect
    <VirtualHost *:80>
        ServerName <server name>
        Redirect permanent / https://<server name>
    </VirtualHost>
```

**HAProxy**

[HAProxy documentation on SSL termination](https://www.haproxy.com/blog/haproxy-ssl-termination/)

_If you intend to use Certbot with HAProxy, please reference the above section "Certbot on HAProxy"_

For HAProxy, you need to have a .pem file that includes both the public certificate and the private key. If you don't already have this, you can simply create a .pem file that contains the appended content of both the .key and .cer/.crt files. Example: `cat mydomain.key mydomain.crt >> output.pem`

Contents of `/etc/haproxy/haproxy.cfg`

```
    frontend
        bind *:80
        bind *:443 ssl crt /etc/ssl/certs/mysite.pem
        http-request redirect scheme https unless { ssl_fc }
        use_backend <backend name> [if {ACL}]

    backend <backend name>
        timeout client          60m
        timeout http-keep-alive 5m
        server <be server name> <BlueBubbles IP>:<BlueBubbles port>
```
