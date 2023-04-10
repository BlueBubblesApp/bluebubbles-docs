# Tailscale Funnel

Tailscale is a mesh VPN software that uses WireGuard technology. It also include other fun features like Tailscale Funnel.

[Tailscale Funnel ](https://tailscale.com/kb/1223/tailscale-funnel/) allows you to publicly expose your machine's local services without needing to purchase a domain & set up port forwarding. It hosts your machine's domain on their Funnel Servers. The Funnel Server accepts requests & sends a TCP proxy to your machine where TLS cert is terminated. Simple, secure & only requires a few short commands.

--- 
Requirements
- [Tailscale account](https://login.tailscale.com/start)
- Enable [HTTPS certificates](https://login.tailscale.com/admin/dns) in the [admin console](https://login.tailscale.com/admin/dns).
- Open the [**Access controls**](https://login.tailscale.com/admin/acls) page in the admin console and click the **Add Funnel to policy** button
---
1. Download Tailscale from the [Mac App Store](https://apps.apple.com/ca/app/tailscale/id1475387142) or [directly from Tailscale](https://pkgs.tailscale.com/stable/#macos) 

2. Login from the top right menu icon & enable start on login from prefrences

3. Add alias for the Tailscale CLI to your shell configuration by entering  the command below into terminal.
```bash
echo 'alias tailscale="/Applications/Tailscale.app/Contents/MacOS/Tailscale"' | sudo tee -a ~/.zshrc
```
Alternatively, you can use `/Applications/Tailscale.app/Contents/MacOS/Tailscale <command>` 

4. Proxy requests to BlueBubbles's local web server on the default port 1234. Make sure to check your setup in case a different port is being used.

```bash
tailscale serve https / http://127.0.0.1:1234
```

5. Enable the funnel to route proxy traffic over Tailscale funnel servers. Supported ports are 443, 8443, or 10000.

```bash
tailscale funnel 443 on
```

6. Check the funnel status with:

```bash
tailscale serve status 
```

7. Finally, add the domain from step 5 to the BlueBubbles Proxy Service drop-down menu:

```bash
https://machine-name.example.ts.net:443/
```

---
- [Tailscale Funnel CLI](https://tailscale.com/kb/1080/cli/#serve)
- [Tailscale Funnel Documentation](https://www.notion.so/Tailscale-Funnel-1ac25b9e1c5d41fda353cdd1e30e032b)
- [Access Control Lists (ACLs)](https://tailscale.com/kb/1018/acls/)
- [Download](https://www.notion.so/Download-Tailscale-client-for-macOS-81a4c0a03d5942de91096a98759d7349)
- [Introduction to Tailscale funnel](https://tailscale.com/blog/introducing-tailscale-funnel/)

Thanks to @bobspop in Discord for creating this guide.
