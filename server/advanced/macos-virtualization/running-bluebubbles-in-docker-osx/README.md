---
description: >-
  This tutorial will show you how you can setup BlueBubbles in a Docker
  container, within a Linux host.
---

# Running BlueBubbles in Docker-OSX

### Contribution Notes

This guide is a contribution by @JustBen on our Discord

### Disclaimer

This guide is not for beginners. However, these steps should make it relatively easy to get up and running with a macOS Docker container running BlueBubbles on a Linux host. Before starting, you should familiarize yourself with the Linux CLI and be at least familiar with containerization and Docker (or Podman).

This guide is provided with no guarantees or warranty. If you encounter any problems, the community may be able to provide help in the Discord (linked in the header of this page), but we cannot guarantee that this will work for every configuration.

This guide has only been tested with MacOS Ventura, though it should work with only minor modifications for older versions.

### Goal

This guide will walk you through setting up BlueBubbles on a macOS VM running in a [Docker-OSX container](https://github.com/sickcodes/Docker-OSX). With the given configuration, your container will be accessible by VNC on port 5999.

### Pre-requisites

You must have all of these already setup/installed in order to continue with the guide.

* A Linux host
* Comfort with bash
* Packages:
  * `docker` (or `podman`)
  * `docker-compose` (or `podman-compose`)
  * `tigervnc` (or another vncviewer)
  * `qemu`
* All dependencies outlined by Docker-OSX in its [Initial Setup documentation](https://github.com/sickcodes/Docker-OSX#initial-setup)
  * As noted in the link above, your system must support virtualization. The Docker-OSX documentation has steps on how to configure and verify that virtualization is supported and enabled on your system.

### Installation

1. Create a directory for all the container resources
2. Create a virtual disk:

```bash
qemu-img create -f qcow2 maindisk.qcow2 128G
```

&#x20; 3\.  Run:

```bash
docker run \
  --rm \
  --rmi \
  --name bluebubbles-setup \
  --dns=1.1.1.1 \
  --device /dev/kvm \
  -p 5999:5999 \
  -v /tmp/.X11-unix:/tmp/.X11-unix \
  -v $PWD/maindisk.qcow2:/image \
  -e IMAGE_PATH="/image" \
  -e EXTRA="-display none -vnc 0.0.0.0:99,password-secret=secvnc0 -object secret,id=secvnc0,data=vncpass" \
  -e DISPLAY=:99 \
  -e WIDTH=1920 \
  -e HEIGHT=1080 \
  -e GENERATE_UNIQUE=true \
  sickcodes/docker-osx:ventura
```

This will take a while because it's downloading the image for Docker-OSX and generating a unique serial number and bootdisk for your mac VM.

You may see a handful of errors particularly related to ALSA. These can be safely ignored.

NOTE: If using Podman as a non-root user, the additional flag `--network slirp4netns:port_handler=slirp4netns` must be added. &#x20;

&#x20;4\.  With your vnc viewer, open a connection to your IP on port 5999. If you're using tigervnc and running this on the same phost, you can use `vncviewer localhost:5999`. The password will be `vncpass` unless you changed it in the command.

&#x20; 5\.  If presented with a boot loader (a black screen with some icons in the center), select the option `MacOS Base System` to boot into the recover/install menu.

&#x20; 6\.  Before installing, you have to format the drive with APFS for a macOS install. Go into Disk Utility and find your drive. Click "Erase". Fill in the name for the drive that you want, then leave the rest as defaults and click "Erase". Close out of Disk Utility.

&#x20; 7\.  Click on "reinstall macOS" and install macOS as normal.

&#x20; 8\.  Once you're on your desktop, test sending an iMessage to yourself. If it does not succeed, it's likely best to restart from the beginning, though our Discord community may be able to help.

&#x20; 9\. **Before shutting down your VM**, run this in a new terminal with the same user:

```bash
docker cp bluebubbles-setup:/home/arch/OSX-KVM/OpenCore/OpenCore.qcow2 ./bootdisk.qcow2
```

This copies the generated bootdisk and serial number information out of the container and saves it as `bootdisk.qcow2` so it can be used for all your future boots.&#x20;

10\.  Shutdown your VM then take down the container.

The container and image will automatically be removed from your system (to keep things clean) because of the `--rm` and `--rmi` options we passed above.

```bash
docker stop bluebubbles-setup
```

&#x20; 11\.  Save this into a file (e.g. `start-bluebubbles.sh`) and make sure you have execute permissions to run it (`chmod +x start-bluebubbles.sh`).

```bash
docker run \
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
```

This new docker run command should cut down on boot time and general overhead.

As before, errors related to ALSA can be safely ignored.

It is HIGHLY RECOMMENDED that you replace `$PWD` in the above command (after both `-v` flags) with an absolute path. For example, replace `$PWD/maindisk.qcow2:/image` with `/home/yourname/bluebubbles/maindisk.qcow2:/image`. This allows you to more easily run this script from any working directory (as you may want to do with a systemd service file).

To change the VNC password, change `data=vncpass` to `data=<yourpassword>` in the `EXTRA` environment variable.

NOTE: As before, if using Podman as a non-root user, the additional flag `--network slirp4netns:port_handler=slirp4netns` must be added.

&#x20; 12\.  Start up the container again (by running the script file you made in step 11). It might take a little while the first time as it pulls down a new container image, but it'll be quicker to boot after this first time. Once it's up, you can connect again with VNC on port 5999 with password `vncpass` (unless you changed it).

&#x20; 13\.  [Install BlueBubbles](https://bluebubbles.app/install/)

&#x20; 14\.  When you want to start the container again, run the script you made in step 11. As before, you can connect to your VM with VNC on port 5999 with `vncpass` or the password you set. To stop the container, run:

```bash
docker stop bluebubbles
```

### Private API notes

* To install the BlueBubbles Private API features, just follow the normal guide [here](https://docs.bluebubbles.app/private-api/installation).
* When disabling SIP, follow the instructions for "Physical Mac, INTEL" on the Private API installation documentation.
  * To clarify, do NOT follow the documentation for [disabling SIP on VMs](https://docs.bluebubbles.app/server/advanced/macos-virtualization/running-a-macos-vm/disabling-sip-on-vms-for-the-private-api#opencore-big-sur). Everything you need is in the standard Private API installation guide.
