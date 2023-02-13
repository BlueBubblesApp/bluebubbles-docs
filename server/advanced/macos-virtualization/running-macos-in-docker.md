# Running macOS in Docker

## Why Docker?

Actually this (`Docker-OSX`) is a QEMU VM running under an Arch Docker. However the setup is streamlined and just more or less copy paste than setting up VM from scratch

## Notes

* All code blocks can be copied into a script to run
* VNC in this setup is not secured with password in ANYWAY, so please use caution and only use local VNC or SSH tunnel to connect to VNC
* There's a `nakedvnc` dockerfile in `Docker-OSX` repo but no prebuilt image. This has proper encrypted (?) VNC setup so if you have resource to compose/build docker image you are welcome to do that.
* Headless option (remove to use head-attached): `-e EXTRA="-display none"`
* Dangerous option: `-p 5999:5999`. This will forward your container VNC port to localhost port. DO NOT EXPOSE THIS PORT TO PUBLIC unless you use `nakedvnc` image with proper encryption (?)
* VNC can be setup with password if you follow the Docker-OSX repo guide, but I won't talk much about it here. SSH tunnel is secure enough for the job.
* Screen `WIDTH/HEIGHT` can be modified freely. 1366x768 works best for me, ymmv.

## Prerequisites Setup

[https://github.com/sickcodes/Docker-OSX#initial-setup](https://github.com/sickcodes/Docker-OSX#initial-setup)&#x20;

`TigerVNC-Viewer` or equivalent VNC Viewer/Client (Optional) `screen` for interacting with docker `-ai`

## Initiate base image:

```
#!/bin/bash
docker run -it \
    --name macos_base \
    --device /dev/kvm \
    -p 50922:10022 \
    -p 5999:5999 \
    -e NOPICKER=true \    
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    -e "DISPLAY=${DISPLAY:-:0.0}" \
    -e EXTRA="-display none -vnc 0.0.0.0:99" \
    -e WIDTH=1366 \
    -e HEIGHT=768 \
    sickcodes/docker-osx:latest
```

* (Headless) connect to server VNC at port localhost:5999 (no password, NOTE: very insecure, local/ssh-tunnel only). For remote (in Local Network) use :5999 to connect
* Install macOS:
  * [Setup disk partition](https://github.com/sickcodes/Docker-OSX#additional-boot-instructions-for-when-you-are-creating-your-container)
  * Install macOS as normal, login with iCloud if needed
  * Note: DO NOT OPEN iMessage yet
* Shutdown in QEMU cmd using `quit`, or shutdown from inside VNC session

## Extract image

Explain: find `mac_hdd_ng.img` from `/var/lib/docker` (make sure file size make sense), copy to current folder

```
#!/bin/bash
sudo cp `sudo find /var/lib/docker -size +10G -name mac_hdd_ng.img | head -1` .
```

## Generate unique serial

```
#!/bin/bash
mkdir tmp && cd tmp
curl https://raw.githubusercontent.com/sickcodes/Docker-OSX/master/custom/generate-unique-machine-values.sh -O
# wget https://raw.githubusercontent.com/sickcodes/Docker-OSX/master/custom/generate-unique-machine-values.sh
chmod +x generate-unique-machine-values.sh
./generate-unique-machine-values.sh --count 1 --output-env ../my_permanent_serial_number.sh
cd ..
rm -rf tmp
```

OR if you already have a working serial configs and just want to migrate to docker solution:

* Create file with `nano my_permanent_serial_number.sh`
* Content of the file

```
export DEVICE_MODEL="DEVICE_MODEL_HERE"
export SERIAL="SERIAL_HERE"
export BOARD_SERIAL="BOARD_SERIAL_HERE"
export UUID="UUID_HERE"
export MAC_ADDRESS="MAC_ADDRESS_HERE"
export WIDTH="1366"
export HEIGHT="768"
```

* `Ctrl+X` then `Y`, `Enter` to save and exit

## First run

```
#!/bin/bash
source ./my_permanent_serial_number.sh
sudo docker run -i \
    --name macos \
    --privileged \
    --device /dev/kvm \
    -v "${PWD}/mac_hdd_ng.img:/image" \
    -p 5999:5999 \
    -p 1234:1234 \
    -p 50922:10022 \
    -e TERMS_OF_USE=i_agree \
    -e NOPICKER=true \
    -e GENERATE_SPECIFIC=true \
    -e DEVICE_MODEL="${DEVICE_MODEL}" \
    -e SERIAL="${SERIAL}" \
    -e BOARD_SERIAL="${BOARD_SERIAL}" \
    -e UUID="${UUID}" \
    -e MAC_ADDRESS="${MAC_ADDRESS}" \
    -e "DISPLAY=${DISPLAY:-:0.0}" \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    -e EXTRA="-display none -vnc 0.0.0.0:99" \
    -e WIDTH="${WIDTH}" \
    -e HEIGHT="${HEIGHT}" \
    sickcodes/docker-osx:naked
```

* Open iMessage and setup iCloud Sync. Use [How to Fix iMessage - Contact Apple Support Guide from tonymacx86](https://www.tonymacx86.com/threads/how-to-fix-imessage.110471/#TOP8) if needed
* Setup BlueBubbles/AirMessage to your liking

## Subsequent run

`screen` is totally optional, I just want to have stdio attached for debugging in QEMU shell

```
#!/bin/bash
screen -dmS macos-docker docker start -ai macos
```

## Post-install Notes/Tips

* SSH port is `50922`, you need to enable macOS SSH from Sharing preferences first.
* `scp` is useful for transferring files from Docker macOS and host
* Read about `ssh -L` for SSH tunneling to make VNC session secure.
