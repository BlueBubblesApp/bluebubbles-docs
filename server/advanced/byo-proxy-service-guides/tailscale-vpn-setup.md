# Tailscale Funnel 
**Requirements**

- Enable [HTTPS certificates](https://login.tailscale.com/admin/dns) in the [admin console](https://login.tailscale.com/admin/dns).
- [Add Funnel to Policy](https://login.tailscale.com/admin/acls) in the admin console.

---

## macOS 

1. Download Tailscale from the [Mac App Store](https://apps.apple.com/ca/app/tailscale/id1475387142) or [directly from Tailscale](https://pkgs.tailscale.com/stable/#macos). Install and log in.
2. Add an alias for the Tailscale CLI to your shell configuration for convenience. Alternatively, you can use `/Applications/Tailscale.app/Contents/MacOS/Tailscale <command>`.

```bash
echo 'alias tailscale="/Applications/Tailscale.app/Contents/MacOS/Tailscale"' | sudo tee -a ~/.zshrc
```

3. Proxy requests to BlueBubbles's local web server on port 1234.

```bash
tailscale serve https / http://127.0.0.1:1234
```

4. Enable the funnel to route proxy traffic over Tailscale funnel servers. Supported ports are 443, 8443, or 10000.

```bash
tailscale funnel 443 on
```

5. Check the funnel status with:

```bash
tailscale serve status
```

6. Finally, add the domain from step 5 to the BlueBubbles Proxy Service drop-down menu:

```bash
<https://machine-name.example.ts.net:443/>
```
--- 
## Run on Host System |  macOS virutal machine

To improve network bandwidth and overall connection, it may be beneficial to run Tailscale Funnel on the host system while using macOS in a virtual environment. To accomplish this, you will need to forward the local port of BlueBubbles running on macOS to the host system and then utilize Tailscale Funnel on the host to handle requests to the BlueBubbles local web server

---
- [Tailscale Funnel CLI](https://tailscale.com/kb/1080/cli/#serve)
- [Tailscale Funnel Documentation](https://www.notion.so/Tailscale-Funnel-1ac25b9e1c5d41fda353cdd1e30e032b)
- [Access Control Lists (ACLs)](https://tailscale.com/kb/1018/acls/)
- [Download](https://www.notion.so/Download-Tailscale-client-for-macOS-81a4c0a03d5942de91096a98759d7349)
- [Introduction to Tailscale funnel](https://tailscale.com/blog/introducing-tailscale-funnel/)