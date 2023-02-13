---
description: >-
  This page will show you multiple options for preventing your mac from
  sleeping. While there are multiple ways, we do have a recommended way... see
  below.
---

# Preventing macOS from Sleeping

## Amphetamine (Recommended)

Amphetamine is a free app on the macOS App Store that allows you to better control when your mac device or VM goes to sleep. With this app, you'll be able to prevent your mac from sleeping due to inactivity, from turning off the display, as well as when you close your laptop lid.

[Download Amphetamine](https://apps.apple.com/us/app/amphetamine/id937984704)

_This solution is the best all-around solution, with the only downside being that you need to download an additional App._

**Note:** Remember to add this to your user accounts login items so it starts up on each reboot.

## Caffeinate (Server Toggle)

Believe it or not, the BlueBubbles server has a feature built into it to prevent your macOS device from sleeping. If you scroll about half-way down the settings page in the server, you should see a toggle for `Keep macOS Awake`. Toggle it on to prevent your mac from sleeping.

<figure><img src="../.gitbook/assets/Capture.PNG" alt=""><figcaption></figcaption></figure>

_This does not prevent your mac from sleeping when you close your laptop lid (if applicable)_

## Battery Settings (System Preferences)

While the previous solutions are great, macOS does have some built-in battery options within System Preferences. Just note, these settings are only available for physical mac devices, not virtualized macOS deployments.

To find these settings, open the `System Preferences` App and find the `Battery` settings. You can also access them by clicking the Battery icon in your macOS status bar, then selecting `Battery Preferences`. Once there, check out the `Battery` and `Power Adapter` settings.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

On the `Battery` page, you may want to configure the settings to **never turn the display off**, as well as disable the `Enable Power Nap while on battery power` toggle.

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

You may want to apply similar configurations to the `Power Adapter` tab. As well as enabling the `Prevent computer from sleeping automatically when the display is off` option.

You may also see a checkbox for `Put hard disks to sleep when possible`. Make sure that checkbox _is not_ checked (disabled).

## Jolt of Caffeine (Alternative)

If you are not having success with the previous solutions, there is another app you can download directly from the macOS App Store to prevent your mac from sleeping. The tool is called "Jolt of Caffeine" and it works very similarly to Amphetamine.

[Download Jolt of Caffeine](https://apps.apple.com/us/app/jolt-of-caffeine/id1437130425)
