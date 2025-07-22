---
description: How to configure your server to enable the Private API
---

# Installation

{% hint style="info" %}
In order to get Private API features, you must disable MacOS extra security measures, called System Integrity Protection (SIP). The reason for this is because Apple does not let us access the internal iMessage code to do things like send reactions if SIP is enabled. When disabled, we can inject a helper process into the iMessage app to call the internal functions for us. In a way, disabling SIP is similar to Jailbreaking your iPhone.
{% endhint %}

{% hint style="info" %}
Disabling SIP can be challenging, we recommend joining our [Discord](https://discord.gg/hFgbV8Jank) to get assistance with the process if you need it!
{% endhint %}

{% hint style="danger" %}
Disable SIP at your own risk! We are not responsible for any damages, issues, or glitches caused by disabling SIP or by using the Private API.

If you use common sense when downloading and installing things, you should be just fine. Please be careful!
{% endhint %}

{% hint style="warning" %}
Disabling SIP on an Apple Silicon Mac disables the ability to install and run _iOS Apps_ on your Mac. If this feature is important to you, don't use the Private API.
{% endhint %}

{% hint style="warning" %}
If you are using a Virtual Machine, please **take a snapshot** before continuing! You will definitely want a failsafe in case something goes wrong.
{% endhint %}

{% hint style="warning" %}
Please ensure you have the latest BlueBubbles Server from [GitHub Releases](https://github.com/BlueBubblesApp/bluebubbles-server/releases)!
{% endhint %}

Now that the warnings are out of the way, lets proceed to the instructions!

## Supported Device Info

The Helper Bundle currently supports macOS 10.13 and up, on both Intel and Apple Silicon Macs.

{% hint style="info" %}
It may support lower macOS versions, but we haven't been able to test them yet.
{% endhint %}

## Instructions

Please follow the instructions for your macOS version in the tabs below.

{% tabs %}
{% tab title="macOS 10.13-10.15 (Sierra-Catalina)" %}
1. Open Terminal on your macOS device
2.  Run the following command to disable Library Validation and enter your password when prompted.

    `sudo defaults write /Library/Preferences/com.apple.security.libraryvalidation.plist DisableLibraryValidation -bool true`
3. Run the following commands to force your Mac to reboot into recovery mode:
   * **WARNING**: _DO NOT do this step or step 4-5_ if you are running dos1dude's patched High Sierra/Mojave/Catalina. It may temporarily brick your Mac until you reset your NVRAM/PRAM
   * **If you are on** _**a real Mac with official macOS**_, do the following:
     1. **WARNING**: This will instantly reboot your Mac. Save everything before executing this command!
     2. `sudo nvram recovery-boot-mode=unused`
     3. `sudo reboot recovery`
   * **If you are using** _**a Virtual Machine or a patched macOS software (Open Core)**_, follow [this guide.](https://docs.bluebubbles.app/server/advanced/running-on-macos-vm/disable-sip-on-vms)
4. When you are booted into `Recovery Mode`:
   * Click on `Utilities` in the top menu bar
   * Select `Terminal`
   * Type this command and hit enter to disable SIP: `csrutil disable`
5. Restart your macOS device/server
   * Click the Apple logo in the top menu, then click `Restart`
6. Turn the Private API switch on in the BlueBubbles Server settings
7. Verify that the Private API is connected by clicking the refresh button inside the **Private API Status** box in BlueBubbles Server settings
8. Go to Settings > Private API Features on the clients you use, and toggle that on. You should now have functioning Private API Features!
{% endtab %}

{% tab title="macOS 11+ (Big Sur+)" %}
1. Open Terminal on your macOS device
2.  Run the following command to disable Library Validation and enter your password when prompted.

    `sudo defaults write /Library/Preferences/com.apple.security.libraryvalidation.plist DisableLibraryValidation -bool true`
3. Follow the instructions below for your device type
   * _**Physical Mac, INTEL, official software**_
     1. Run the following commands to force your Mac to reboot into recovery mode:
        * **WARNING**: This will instantly reboot your Mac. Save everything before executing these commands!
        * `sudo nvram internet-recovery-mode=RecoveryModeDisk`
        * `sudo reboot recovery`
     2. When you are booted into `Recovery Mode`:
        * Click on `Utilities` in the top menu bar
        * Select `Terminal`
        * Type this command and hit enter to disable SIP: `csrutil disable`
     3. Click the Apple logo in the top menu bar, then click `Restart`
   * _**Physical Mac, APPLE SILICON, official software**_
     1. Do the following to force your Mac to reboot into recovery mode:
        * Shut down the Mac normally
        * Press and hold the power button on your Mac until you see "Loading startup options."
        * Click Options, then click Continue, and enter the admin password if requested.
     2. When you are booted into `Recovery Mode`:
        * Click on `Utilities` in the top menu bar
        * Select `Terminal`
        * Type this command and hit enter to disable SIP: `csrutil disable`
     3. Click the Apple logo in the top menu bar, then click `Restart`
   * _**macOS on a Virtual Machine or patched macOS software on a Physical Mac**_
     1. Follow [this guide.](https://docs.bluebubbles.app/server/advanced/running-on-macos-vm/disable-sip-on-vms)
4. Turn the Private API switch on inside the BlueBubbles Server settings
5. Verify that the Private API is connected by clicking the refresh button inside the **Private API Status** box in BlueBubbles Server settings
6. Go to Settings > Private API Features on the clients you use, and toggle that on. You should now have functioning Private API Features!
{% endtab %}
{% endtabs %}

## Troubleshooting

Here are some basic troubleshooting steps. Please try these out, and if you need more help, feel free to join our [Discord](https://discord.gg/BdsGwREh)!

* Make sure you have the Private API switch turned on in _**both**_ the server and the client app
* Try force quitting and reopening the server (with private API switch toggled on)
* Run `csrutil status` inside Terminal, then join our Discord and let us know what the output is.

If none of this works, you should join our Discord and the developers will be able to help you out. In your post, please include your macOS version, Mac chipset (Intel / Apple Silicon), and your server version. Thanks!
