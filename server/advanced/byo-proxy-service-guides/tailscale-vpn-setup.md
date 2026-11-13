# Tailscale Funnel

Tailscale is a mesh VPN software that uses WireGuard technology. It also include other fun features like Tailscale Funnel.

[Tailscale Funnel ](https://tailscale.com/kb/1223/tailscale-funnel/) allows you to publicly expose your machine's local services without needing to purchase a domain & set up port forwarding. It hosts your machine's domain on their Funnel Servers. The Funnel Server accepts requests & sends a TCP proxy to your machine where TLS cert is terminated. Simple, secure & only requires a few short commands.

--- 
Requirements
- [Tailscale account](https://login.tailscale.com/start)
- Tailscale v1.52 or later
- [MagicDNS](https://login.tailscale.com/admin/dns) enabled for your tailnet
---
1. Download Tailscale from the [Mac App Store](https://apps.apple.com/ca/app/tailscale/id1475387142) or [directly from Tailscale](https://pkgs.tailscale.com/stable/#macos) 

2. Login from the top right menu icon & enable start on login from preferences

3. Add alias for the Tailscale CLI to your shell configuration by entering  the command below into terminal.
```bash
echo 'alias tailscale="/Applications/Tailscale.app/Contents/MacOS/Tailscale"' | sudo tee -a ~/.zshrc
```
Alternatively, you can use `/Applications/Tailscale.app/Contents/MacOS/Tailscale <command>` 

4. Start a Funnel that proxies to the BlueBubbles local web server on its default port, `1234`. If your server uses a different local port, replace `1234`. Funnel can listen publicly on ports 443, 8443, or 10000.

```bash
tailscale funnel --bg --https=443 1234
```

{% hint style="warning" %}
If you followed an older version of this guide, first inspect both `tailscale serve status` and `tailscale funnel status` for a persisted Serve route on port 443. Running the Funnel command above makes Funnel the active mode for that public port; confirm the route afterward with `tailscale funnel status`. Do not use a blanket `tailscale serve reset` as a migration shortcut because it also removes unrelated Serve routes on the machine.
{% endhint %}

The first time you run this command, Tailscale opens a browser page for you to approve Funnel. After approval, Tailscale automatically provisions the HTTPS certificate and adds the default Funnel node attribute to your tailnet policy.

{% hint style="info" %}
If the approval page does not open or the policy update fails, open [**Access controls**](https://login.tailscale.com/admin/acls), expand the **Funnel** section, and select **Add Funnel to policy**. This manual step is normally unnecessary.
{% endhint %}

5. Check the Funnel status. The output lists the public URL and its route to your local BlueBubbles server:

```bash
tailscale funnel status
```

6. Copy the entire URL shown in step 5. In BlueBubbles, select **Dynamic DNS / Custom URL** from the **Proxy Setup** dropdown and enter that URL:

```bash
https://machine-name.example.ts.net/
```

---
- [Tailscale Funnel CLI](https://tailscale.com/docs/reference/tailscale-cli/funnel)
- [Tailscale Serve CLI](https://tailscale.com/docs/reference/tailscale-cli/serve)
- [Tailscale Funnel Documentation](https://tailscale.com/docs/features/tailscale-funnel)
- [Access Control Lists (ACLs)](https://tailscale.com/kb/1018/acls/)
- [Download](https://tailscale.com/download/mac)
- [Introduction to Tailscale funnel](https://tailscale.com/blog/introducing-tailscale-funnel/)

Thanks to @bobspop in Discord for creating this guide. Updated by @ampersandru 
