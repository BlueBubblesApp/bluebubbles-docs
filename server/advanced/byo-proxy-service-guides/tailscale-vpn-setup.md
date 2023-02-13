---
description: >-
  This guide will show you how you can use your existing Tailscale VPN account
  to connect to your BlueBubbles Server (via a Funnel), without port-forwarding
---

# Tailscale VPN Setup

1. [Download the Tailscale client on macOS](https://tailscale.com/kb/1016/install-mac/) and log in.
2. [Enable HTTPS in the Tailscale admin console and add the "funnel" attribute under "nodeAttrs" in your Tailnet policy file.](https://tailscale.com/kb/1223/tailscale-funnel/#setup)
3. [Add Tailscale as an alias to your `.bashrc`, `.zshrc`, or shell config to make it easier](https://tailscale.com/kb/1080/cli/#using-the-cli): `alias tailscale="/Applications/Tailscale.app/Contents/MacOS/Tailscale"`&#x20;
4. In the terminal, enter the command `tailscale serve tcp --terminate-tls 1234` (use the Bluebubbles local port). The Bluebubbles server will be served by port 443 as the default, but ports 8443 or 10000 can also be used by adding the flag `-serve-port 8443 / 10000`
   * Reference: [https://tailscale.com/kb/1080/cli/#serve](https://tailscale.com/kb/1080/cli/#serve)
5. Turn Tailscale Funnel on by running `tailscale serve funnel on`.&#x20;
6. Check the funnel status by running `tailscale serve status` or in more detail by`tailscale server status --json`.&#x20;
7. Configure Bluebubbles to use the domain listed by the command above, including the port (default 443).&#x20;
8. Wait for Tailscale to propagate their DNS with your domain (estimated time 10 minutes to 24 hours).&#x20;
9. Done. Tailscale will take care of certification at [https://yournode.tailscaledomain.ts.net:443/](https://yournode.tailscaledomain.ts.net/) and send it to 127.0.0.1:1234. No port forwarding is necessary.

Thanks to @bobspop in Discord for creating this guide.
