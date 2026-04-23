# Deploying macOS in VMWare on Windows (Full Guide)

This document will guide you on how to setup a macOS virtual machine on your Windows operating system. This guide has a lot of steps, but I promise it's not hard. It's mainly to provide all the context and information to make the setup easy to follow. I've also broken the steps down into multiple sections in order to make it easier to understand.

### Before You Begin

Make sure that your PC has Virtualization enabled in the BIOS. If you do not know if you have virtualization enabled, open Task Manager's `Performance` tab:

<figure><img src="../../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

If virtualization is _disabled_, you need to boot into your PC's BIOS and enable it. Now, enabling it will be different per-vendor, however, here are 2 YouTube videos that may help:

* [https://www.youtube.com/watch?v=ZDeje9wgDp4](https://www.youtube.com/watch?v=ZDeje9wgDp4)
* [https://www.youtube.com/watch?v=MOuTxfzCvMY](https://www.youtube.com/watch?v=MOuTxfzCvMY)

### Downloading a Fresh Copy of macOS

The first step in setting up a virtual machine is getting an image that is compatible with our virtualization software, in this case, VMWare. To do this, we will use OpenCore. The following instructions are a shortened form of the full OpenCore Install Guide:

[https://dortania.github.io/OpenCore-Install-Guide/installer-guide/windows-install.html](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/windows-install.html)

**Note**: This method is preferred over downloading an existing ISO from the internet because you never know when the integrity of an ISO from the internet might be compromised.

#### Pre-requisites

* You should have Python installed: [https://www.python.org/downloads/](https://www.python.org/downloads/)

#### Instructions

1. Download the latest release of OpenCore.
   * Download Link: [https://github.com/acidanthera/OpenCorePkg/releases](https://github.com/acidanthera/OpenCorePkg/releases)
   * Choose the `zip` file labeled with `RELEASE`.
2. Extract the OpenCore ZIP file, and open the extracted folder.
3. Navigate into `Utilities\macrecovery`.
4. Holding `Shift` on your keyboard, right-click your explorer window and select `Open PowerShell window here`.
5.  Run the python command corresponding to the macOS version you want to download, found in the OpenCore Install Guide.

    * [https://dortania.github.io/OpenCore-Install-Guide/installer-guide/windows-install.html#downloading-macos](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/windows-install.html#downloading-macos)
    * Running the command will download the `BaseSystem.dmg` image from Apple's servers

    {% hint style="info" %}
    **macOS Ventura**

    If you'd like to download macOS Ventura, use the command below:

    **This is not recommended for inexperienced users!**

    ```bash
    python ./macrecovery.py -b Mac-27AD2F918AE68F61 -m 00000000000000000 download
    ```
    {% endhint %}
6. Next, cut/copy the `BaseSystem.dmg` file (generated in the `macrecovery` folder), to your user profile's `Documents` folder

### Converting the macOS System Image to a VMWare Disk

Now that you have the `BaseSystem.dmg` file, we will now need to convert it into a format that VMWare can understand and load. In order to do that, we need to use yet another open-source tool called `QEMU`. Follow the steps below to learn how to use it.

#### Installing QEMU

This is a short guide to show you how to install `QEMU`, an open-source tool for working with disk images.

1. Download `QEMU` for Windows.
   * Download Link: [https://qemu.weilnetz.de/w64/](https://qemu.weilnetz.de/w64/)
   * You will see a bunch of folders with years on them. Ignore those. Download the `qemu-w64-setup-XXXXXXXX.exe` installer.
2. Run the installer once the download completes.
   * If you get a Windows SmartScreen alert, allow the installer to `Run Anyways`.
   * If you get an error during installation saying a file cannot be written, just click the `Retry` button in the prompt.
   * The installer will install files to `C:\Program Files\qemu`
3. (Optional). You may want to add the `C:\Program Files\qemu` path to your Windows System Environment Variables
   * This is optional because I will be using the full path to `qemu-img` in the rest of the guide.
   * If you are non-technical or a beginner, I would skip this step.

#### Creating the VMDK

Now that we've installed `QEMU`, we need to use it to convert the `BaseSystem.dmg` to a VMWare `.vmdk` disk image.

1. Open `Explorer` and navigate into your `Documents` folder (where your `BaseSystem.dmg` is located).
2. Holding `Shift` on your keyboard, right-click your explorer window and select `Open PowerShell window here`.
3. Run the following command to use `QEMU` to convert the image to a `.vmdk`.
   * `& "C:\Program Files\qemu\qemu-img.exe" convert -O vmdk -o compat6 BaseSystem.dmg recovery.vmdk`
     * Reference: [https://www.insanelymac.com/forum/topic/342603-guide-simple-steps-to-create-macos-installer-for-vmware-on-linux-or-windows/](https://www.insanelymac.com/forum/topic/342603-guide-simple-steps-to-create-macos-installer-for-vmware-on-linux-or-windows/)
4. You will now have a `recovery.vmdk` file in your `Documents` folder that you can use with VMWare.

### Preparing & Unlocking VMWare

In order for you to even be able to load a macOS system into VMWare, you will need to use a third-party program called `Auto-Unlocker` to patch your VMWare installation.

#### Pre-requisites

* You should have either VMWare Workstation Player (free), or VMWare Workstation Pro (paid) installed
  * VMWare Workstation Player (free): [https://www.vmware.com/products/workstation-player.html](https://www.vmware.com/products/workstation-player.html) ([VMWare Workstation Player 16 download](https://support.broadcom.com/group/ecx/productfiles?subFamily=VMware%20Workstation%20Player&displayGroup=VMware%20Workstation%20Player%2016&release=16.2.5&os=&servicePk=208718&language=EN))
  * VMWare Workstation Pro (paid): [https://www.vmware.com/products/workstation-pro.html](https://www.vmware.com/products/workstation-pro.html)

#### Instructions (VMWare 16)

1. Download `Auto-Unlocker`.
   * [https://github.com/paolo-projects/auto-unlocker/releases/tag/v2.0.0a](https://github.com/paolo-projects/auto-unlocker/releases/tag/v2.0.0a)
2. Extract the downloaded `zip` file.
3. Run the `Unlocker.exe` file.
   * This will bring up a GUI to patch your VMWare installation
   * The app will attempt to auto-fill the install locations. However, if they are empty, please locate and select your VMWare installation folder.
4. Click the `Patch` button
5. Once the patching is complete, you may close the Unlocker app.

#### Instructions (VMWare 15 or Older)

1. Download `Unlocker`.
   * [https://github.com/paolo-projects/unlocker/releases/tag/3.0.4](https://github.com/paolo-projects/unlocker/releases/tag/3.0.4)
2. Extract the downloaded `zip` file.
3. Run the `win-install.cmd` file _as an Administrator_

### Setting up the Virtual Machine

#### Creating the Virtual Machine

This section will detail how to create the base virtual machine using the recovery disk we created earlier.

**Pre-requisites**

* You should have either VMWare Workstation Player (free), or VMWare Workstation Pro (paid) installed
  * VMWare Workstation Player (free): [https://www.vmware.com/products/workstation-player.html](https://www.vmware.com/products/workstation-player.html) ([VMWare Workstation Player 16 download](https://support.broadcom.com/group/ecx/productfiles?subFamily=VMware%20Workstation%20Player&displayGroup=VMware%20Workstation%20Player%2016&release=16.2.5&os=&servicePk=208718&language=EN))
  * VMWare Workstation Pro (paid): [https://www.vmware.com/products/workstation-pro.html](https://www.vmware.com/products/workstation-pro.html)
* You should have patched your VMWare installation using the Unlocker app

**Instructions**

1. Open VMWare and click `File -> New Virtual Machine`
2. Select `Custom Installation` and start navigating through the New Virtual Machine setup screens.
3. On the `Guest Operating System Installation` page, select, `I will install the operating system later.`
4. On the `Select a Guest Operating System` page, select `Apple Mac OS X`, as well as the corresponding macOS version.
5. On the `Processor Configuration` page, select `1` for the `Number of processors`.
   * For the `Number of cores per processor`, select a number that is within your computer's resource constraints.
6. On the `Memory for the Virtual Machine` page, we recommend the following:
   * Minimum: 4096 MB
   * Recommended: 8096 MB
7. On the `Network Type` page, select `Use network address translation (NAT)`.
8. On the `Select a Disk` page, select `Use an existing virtual disk`.
9. On the `Select an Existing Disk` page, browse & select the `recovery.vmdk` disk we created earlier.
10. Finish the setup.
11. Edit your Virtual Machine's settings.
12. Add a new piece of hardware.
    * On the `Hardware Type` page, select `Hard Disk`.
    * On the `Disk Type` page, select `SATA`.
    * On the `Select a Disk` page, select `Create a new virtual disk`
    * On the `Specify Disk Capacity` page, enter an amount that makes sense
      * Minimum: 50 GB
      * Recommended: 80 GB
    * Complete the Hard Disk setup.

#### Patching the Virtual Machine (iServices)

This section will detail how to patch your virtual machine in order to properly run macOS and get iServices setup.

1. Navigate to your virtual machine's files
   * The virtual machine's files are located here: `%userprofile%\Documents\Virtual Machines\`
2. Create a backup of your virtual machine's `.vmx` file
   * You can just copy and paste it into the same directory, appending `.bak` to the filename.
3. Open your virtual machine's original `Configuration File` (`.vmx`) in a text editor (i.e. Notepad)
4. Paste the following line into the `.vmx` file and save it.

```vmx
smc.version = "0"
```

* If you have an AMD CPU, also add the following to your `.vmx` file.

```vmx
cpuid.0.eax = "0000:0000:0000:0000:0000:0000:0000:1011"
cpuid.0.ebx = "0111:0101:0110:1110:0110:0101:0100:0111"
cpuid.0.ecx = "0110:1100:0110:0101:0111:0100:0110:1110"
cpuid.0.edx = "0100:1001:0110:0101:0110:1110:0110:1001"
cpuid.1.eax = "0000:0000:0000:0001:0000:0110:0111:0001"
cpuid.1.ebx = "0000:0010:0000:0001:0000:1000:0000:0000"
cpuid.1.ecx = "1000:0010:1001:1000:0010:0010:0000:0011"
cpuid.1.edx = "0000:0111:1000:1011:1111:1011:1111:1111"
```

{% hint style="warning" %}
**macOS Ventura**

If your VM is macOS Ventura, you may need to add **(or edit)** the following to enable internet connectivity:

```vmx
ethernet0.virtualDev = "vmxnet3"
```
{% endhint %}

#### Booting the Virtual Machine

Once you've edited the `.vmx` file, you should be able to boot the virtual machine normally.

**Common Issues**

If you are having issues performing the initial boot for the virtual machine, take a look at the most common issues below.

* **Stuck on Apple Boot Logo**: If you attempt to boot your virtual machine and it's stuck on the Apple Boot Logo, do the following to fix the issue. Power off the Virtual Machine, then open the virtual machine's settings. Once the settings window opened beside the `Hardware` tab click on `Options`. Change the `Apple Mac OS X` selection to `Microsoft Windows` then click OK. Power on the virtual machine again. Once all installed then go back to settings and set it back to `Apple Mac OS X`
* **The CPU has been disabled by the guest operating system**: Enable virtualization in your computer's BIOS
  * There are guides on YouTube describing how to do this.
* **Feature 'cpuid.ds' was absent, but must be present**: This is due to a corrupted vmx file. Try using a backup of your `.vmx` file and edit the `.vmx` file carefully this time.
  * Make sure that your editor is _not_ converting the quotes (`"`) to "greek" quotes
* **This virtual machine requires AVX2 but AVX is not present. This virtual machine cannot be powered on**: This is due to a corrupted vmx file. Try using a backup of your `.vmx` file and edit the `.vmx` file carefully this time.
  * Make sure that your editor is _not_ converting the quotes (`"`) to "greek" quotes
* **Module 'featurecompat' power on failed**: In your `.vmx` file, make sure that your editor is _not_ converting the quotes (`"`) to "greek" quotes

#### Installing macOS

Once you're able to boot into the recovery setup, follow these steps to properly install macOS.

**Note**: You may want to power off your virtual machine and take a snapshot before continuing. If anything goes wrong during setup, you can always revert back to your snapshot.

1. Select `Disk Utility` and hit `Continue`.
2. On the `Disk Utility` page, select the Hard Disk you created.
   * This is _not_ the one labeled, `macOS Base System`
3. With the Hard Disk selected, click `Erase` in the top right of Disk Utility.
   * This will format the hard disk into a format that macOS can use.
4. Close Disk Utility.
5. On the recovery page, select `Reinstall macOS <version>` and hit `Continue`.
6. Continue through the setup, accepting the license agreement(s).
7. When prompted to select the disk to install macOS, select the disk you just formatted.
8. Wait for the operating system to be installed.
9. Once the operating system is installed, navigate through the macOS setup.
   * You _do not_ need to sign into an Apple ID when prompted.
10. Create your macOS computer account, finish the setup, and login.

**Note**: Once everything is setup, you may edit the Virtual Machine's settings and remove the `recovery.vmdk` Hard Disk. Do not delete the Hard Disk you created manually (to store the install). **Note 2**: You may want to power off your virtual machine and take another snapshot before continuing.

#### Fixing iServices

By default, your macOS deployment will _not_ support iServices such as iMessage. In order to support iServices, additional configurations must be done. We will accomplish this by utilizing a tool called `Clover Configurator`.

1. Boot up your macOS virtual machine and login.
2. Open Safari and download `Clover Configurator`.
   * Website: [https://mackie100projects.altervista.org/download-clover-configurator/](https://mackie100projects.altervista.org/download-clover-configurator/)
3. Open `Clover Configurator`.
   * You may need to allow it to run via `System Preferences -> Security & Privacy`.
4. Once opened, navigate to the `SMBIOS` tab in the sidebar.
5. Find the up/down arrow button under the large "question mark" image, and to the right of the `Update Firmware Only` checkbox.
6. Click on the up/down arrow button and select any product made in 2019 or newer.
   * We recommend `MacPro7,1` or `iMac16,1`
7. Copy the following configuration to an editor (i.e. Notepad), on your host computer (Windows).

```config
board-id = "AAA"
hw.model.reflectHost = "FALSE"
hw.model = "BBB"
serialNumber.reflectHost = "FALSE"
serialNumber = "CCC"
smbios.reflectHost = "FALSE"
efi.nvram.var.ROM.reflectHost = "FALSE"
efi.nvram.var.MLB.reflectHost = "FALSE"
efi.nvram.var.ROM = "DDD"
efi.nvram.var.MLB = "EEE"
```

1. In Clover Configurator, click the `Generate New` button next to the `Serial Number` field to generate a new serial number.
2. Copy & paste the following Clover Configurator SMBIOS fields into the configuration from above:
   * `AAA`: Replace with your `Board ID` (i.e. `Mac-XXXXXXXXXXXXXX`)
   * `BBB`: Replace with your `Product Name` (i.e. `MacPro7,1`)
   * `CCC`: Replace with your generated `Serial Number`
3. In Clover Configurator, click on the `Rt Variables` tab in the sidebar.
4. Click on the `Generate` button next to the `ROM` text.
5. Copy & paste the following Clover Configurator Rt Variable fields into the configuration from above:
   * `DDD`: Replace with your generated `ROM`
   * `EEE`: Replace with your generated `MLB`
6. Close Clover Configurator & shut down your macOS virtual machine
7. Open your virtual machine's `.vmx` file. The same one you edited earlier in the setup guide.
   * The virtual machine's files are located here: `C:\Users\<username>\Documents\Virtual Machines\`
8. Paste your completed configuration above into the `.vmx` file.
9. Find the `board-id.reflectHost` configuration and verify that it is set to `"TRUE"`.
10. Find the `ethernet0.addressType` configuration and change it from `"generated"` to `"static"`
11. In your browser, go to the following link to grab an "OUI" for the configuration:
    * [https://hwaddress.com/company/apple-inc/](https://hwaddress.com/company/apple-inc/)
    * The OUI contains 3 segments, each containing 2 characters.
    * It does not matter which OUI you choose. Choose any.
12. Find the `ethernet0.generatedAddress` configuration and change the first 3 segments to match the 3 segments of your chosen OUI.
    * Example: If my current `generatedAddress` is `00:0c:29:bb:91:7f`, and I've selected the OUI, `00-1F-F3`, my new `generatedAddress` will be `00:1f:f3:bb:91:7f`
    * It's recommended to use lower-case characters for the alphabetical letters.
13. Rename the `ethernet0.generatedAddress` configuration to just `ethernet0.Address`
14. Replace the `ethernet0.generatedAddressOffset = "0"` configuration with `ethernet0.checkMACAddress = "FALSE"`
15. Save and close the `.vmx` file.

Your macOS virtual machine should be good to go! You now should be able to sign into iMessage and use the other iServices. Boot up your virtual machine and sign into iMessage to verify everything is working correctly.

**Note**: If something failed during setup and your macOS virtual machine will not boot, restore from an earlier snapshot and redo part of the setup!

#### Final Checks

Here are a few final checks to make sure that your macOS virtual machine is setup correctly.

* When the macOS virtual machine is booted & running, click on the `Apple Logo` and select `About this Mac`. Verify that the serial number listed matches the serial number you configured in the steps above.
* Open `System Preferences` and open your `Date & Time` settings. Verify that your timezone is set correctly and your time is properly synced.
  * If you are unsure, you can open Safari and go to [https://time.is](https://time.is) to see if your time synchronization is misaligned.
  * If your time synchronization is misaligned, you can synchronize it by opening `Terminal` and running this command: `sudo ntpdate -vu time.apple.com`
