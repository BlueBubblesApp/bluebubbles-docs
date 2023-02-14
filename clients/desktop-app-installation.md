---
description: How to install the desktop app from Github Releases
---

# Desktop App Installation

{% embed url="https://github.com/bluebubblesapp/Bluebubbles-app/releases" %}
Github Releases
{% endembed %}

## Windows Users

### Microsoft Store

{% embed url="https://www.microsoft.com/store/productId/9P3XF8KJ0LSM" %}
Microsoft Store Download
{% endembed %}

### Standalone Executable (Windows)

1. Download `bluebubbles_standalone.exe` from GitHub releases (linked above)
2. Run the installer
3. You may receive a warning from Windows Defender about an unrecognized publisher, simply click "More Info", and then "Run anyways" at the bottom.
4. Complete the installation

## Linux Users

### Flatpak

{% embed url="https://flathub.org/apps/details/app.bluebubbles.BlueBubbles" %}
BlueBubbles on Flathub
{% endembed %}

### Standalone Executable (Linux)

1. Download `bluebubbles_linux.zip` from GitHub Releases (linked above)
2. Unzip `bluebubbles_linux.zip`
3. Move `libobjectbox.so` from `bundle/lib` in bundle to `/usr/lib`
4. Run the executable `bluebubbles_app` inside `bundle`

Note: You might need to install the following dependencies:&#x20;

**For notifications** - `libnotify-dev`&#x20;

**For video playback** - `vlc`, `libvlc-dev`

**For the system tray** - `appindicator3-0.1`, `libappindicator3-dev`

You can use the following command:

```bash
sudo apt-get -y install libnotify-dev vlc libvlc-dev appindicator3-0.1 libappindicator3-dev
```
