---
description: A way to get https when portforwarding with bluebubbles
---

# Caddy & DuckDNS

This guide is written with the intention caddy is deployed on the same mac as your bluebubbles server but the process is similar if you have a remote caddy server.

Benefits:

* Works nonstandard ports

### Requirements

* A duckdns account with a domain [https://www.duckdns.org/domains](https://www.duckdns.org/domains)
* A setup bluebubbles server
* The ability to portforward your mac mini or reverse proxy to a machine that can be portforwarded (ex: aws, gcp, oracle, etc)

### Steps to setup caddy

1. Download caddy with duckns and move it to your home folder with the following command

Intel Macs:

`curl -o ./caddy https://caddyserver.com/api/download?os=darwin&arch=amd64&p=github.com/caddy-dns/duckdns&idempotency=8875705962096`

Apple Silicon:

`curl -o ./caddy https://caddyserver.com/api/download?os=darwin&arch=arm64&p=github.com/caddy-dns/duckdns&idempotency=28552737821716`

1. Make caddy executable with the command `chmod 755 ./caddy`
2. Download the template caddy file with the following command:

`curl -o ./Caddyfile https://raw.githubusercontent.com/Rihcus/BB-dynamic-dns-duckdns/main/Caddyfile`

1. Edit the Caddyfile

* replace the example domains with your domains
* replace `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` with your duckdns token
* Optional adjust the https/http or bluebubbles server ports as needed

1. For testing purposes set your duckdns domain to the local ip of your bluebubbles server using the duckdns web portal

{% embed url="https://www.duckdns.org/domains" %}

1. test the config with `./caddy run`
2. if the config works and you can access your domain via https use `./caddy start` to make it auto run
3. Setup portforwarding (not covered in this guide)
4. Optional install duckdns to your mac server in order to auto-update public ip

{% embed url="https://www.duckdns.org/install.jsp" %}

#### Credit

Guide created by @Deemo in the BlueBubbles Discord
