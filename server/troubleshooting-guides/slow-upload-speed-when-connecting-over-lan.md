---
description: >-
  This document will help you fix slow upload speeds when connecting to your
  BlueBubbles Server over your LAN.
---

# Slow Upload Speed When Connecting Over LAN

### What's the issue?

When sending attachments via BlueBubbles, upload speeds are incredibly slow when connected over your LAN (localhost/local IP)

### What's the cause?

This issue only occurs in VMware deployments with `vmxnet3` is used as the network adapter.

### What's the fix?

To fix this issue, you just need to disable "TCP Segment Offload (TSO)" on your Mac.

1. Open `Terminal` on your Mac
2. Run the following command: `sudo sysctl -w net.inet.tcp.tso=0`
3. Create the following file (if not created): `/etc/sysctl.conf`
4. Add the following line to the `sysctl.conf` file: `net.inet.tcp.tso=0`
