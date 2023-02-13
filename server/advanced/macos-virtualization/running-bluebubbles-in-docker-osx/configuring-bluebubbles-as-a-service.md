---
description: >-
  This guide will show you how to setup the BlueBubbles Docker container to run
  as a service on your Linux host. This is to make sure that the host starts up
  when your Linux host restarts.
---

# Configuring BlueBubbles as a Service

### BlueBubbles as a systemd service

### Introduction

If you do not have a physical Mac at your disposal and if you already have a Linux home server, it is relatively easy to turn BlueBubbles into a systemd service which you can configure to run at startup. This way, when your home server starts or reboots, it BlueBubbles automatically starts up.

BlueBubbles does have to be initially configured manually, including a full install of a macOS VM. However, using Docker-OSX makes this configuration much easier. If you are not already running BlueBubbles in a Docker-OSX container, first follow our guide on Configuring BlueBubbles in a container with Docker-OSX.

### Guide

Once you are done configuring Docker-OSX and your BlueBubbles install:

#### Inside the VM

1. [Enable auto-login](https://support.apple.com/en-us/HT201476). This will make the boot faster and allow the BlueBubbles server to start automatically.
2. In your BlueBubbles server settings, enable "Startup with MacOS".
3. If using the Private API, ensure that MacForce launches at start:

MacForge Preferences > "General" tab > Check "Launch MacForce at Login"

#### On your Linux host

1. Create a new systemd service:

```bash
sudo systemctl edit --force --full bluebubbles.service
```

&#x20; 2\.  Enter the following:

```bash
[Unit]
Description=BlueBubbles iMessage relay server
Requires=docker.service
After=docker.service

[Service]
Restart=always
ExecStart=/usr/bin/docker run \
  --rm \
  --name bluebubbles \
  --dns=1.1.1.1 \
  --device /dev/kvm \
  -p 5999:5999 \
  -p 1234:1234 \
  -p 50922:10022 \
  -v /tmp/.X11-unix:/tmp/.X11-unix \
  -v $PWD/maindisk.qcow2:/image \
  -v $PWD/bootdisk.qcow2:/bootdisk \
  -e IMAGE_PATH="/image" \
  -e BOOTDISK="/bootdisk" \
  -e EXTRA="-display none -vnc 0.0.0.0:99,password-secret=secvnc0 -object secret,id=secvnc0,data=vncpass" \
  -e ADDITIONAL_PORTS="hostfwd=tcp::1234-:1234," \
  -e DISPLAY=:99 \
  -e WIDTH=1920 \
  -e HEIGHT=1080 \
  -e NOPICKER=true \
  sickcodes/docker-osx:naked
ExecStop=/usr/bin/docker container stop bluebubbles

[Install]
WantedBy=multi-user.target
```

It's recommended that you replace the value of `ExecStart` with the script you made in Step 11 of the _Configuring BlueBubbles in a container with Docker-OSX_ guide. If you do choose to paste the above code block into your new systemd service file, you will at least need to replace `$PWD` (in the `-v` options) with the full path to your Docker OSX virtual disks.

Alternatively, it is possible to point to the script file you saved. In that case, replace the `ExecStart` line with this instead:

```bash
ExecStart=/usr/bin/sh /absolute/path/to/your/script.sh
```

NOTE: If using podman, replace `docker.service` with `podman.service` and the `docker run` command with `podman run`.

&#x20; 3\.  Reload the systemd daemons:

```bash
sudo systemctl daemon-reload
```

&#x20; 4\.  Enable the service to start it at boot, and activate it now:

```bash
sudo systemctl enable --now bluebubbles.service
```
