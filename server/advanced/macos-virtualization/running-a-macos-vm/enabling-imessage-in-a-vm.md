---
description: How to enable iMessage after successfully creating an unofficial Mac device
---

# Enabling iMessage in a VM

iServices are 99% likely to not run on your unofficial Mac until you successfully spoof the required items to disguise your VM as a real Mac.

## OpenCore

If you made your macOS install using OpenCore, follow their [guide ](https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html)to enable iServices.

## VMWare

If you made your macOS inside VMWare, here is how you can enable iServices:

{% hint style="warning" %}
This guide is tailored to work on Catalina _only_. We recommend using Catalina initially and upgrading to Big Sur later because Apple changed how iMessage activation worked in Big Sur.
{% endhint %}

{% hint style="warning" %}
#### AMD USERS PLEASE READ

If you followed an AMD guide that required you to change your VMX file so that the VM will boot, **you will need to use the model number that the guide used, along with other modifications to the VMX.** \
****Our guides suggest using `MacBookPro14,3` - use this in GenSMBIOS. \
The `board-id` for `MacBookPro14,3` is `Mac-551B86E5744E2388`. \
You should also use `smbios.reflectHost = "FALSE"` instead of `TRUE`.
{% endhint %}

{% hint style="info" %}
If you would like to use a _lower_ macOS version than Catalina, you will need to use a different model number than `MacPro7,1` in step 1 of the guide. This is because that model initially launched with Catalina, so it does not support lower macOS versions. A good model to try that supports High Sierra and higher is `iMacPro1,1`.
{% endhint %}

1. Follow the OpenCore guide linked above, but only complete the following sections (make sure to save all information from these sections for later use):
   * **Using GenSMBIOS**
     * You will want to use the model number `MacPro7,1`. Verify your serial numbers using the [Apple Check Coverage Page.](https://checkcoverage.apple.com/) You have got a good serial number when the page says **Purchase Date not Validated**.
       * We recommend to use a browser with TOR functionality for this step, such as Brave Browser (use "New private window with Tor" option). This is so Apple does not rate limit you when checking serials (TOR randomizes your IP address and location every time you load the page, at the cost of network speed).
   * **Choose a Mac Address**
   * **Derive the corresponding ROM Value**
2.  Open up a Notepad window, and type the following information (you will need this later):\
    \
    (AMD USERS PLEASE MAKE SURE TO READ THE ORANGE NOTE ABOVE)\


    ```
    board-id = "Mac-27AD2F918AE68F61" 
    hw.model.reflectHost = "FALSE" 
    hw.model = "MacPro7,1" 
    serialNumber.reflectHost = "FALSE" 
    serialNumber = "<YOUR GENERATED SERIAL NUMBER>" 
    smbios.reflectHost = "FALSE"
    efi.nvram.var.ROM.reflectHost = "FALSE"
    efi.nvram.var.MLB.reflectHost = "FALSE"
    efi.nvram.var.ROM = "<YOUR GENERATED ROM VALUE>"
    efi.nvram.var.MLB = "<YOUR GENERATED BOARD SERIAL (AKA MLB)>"
    ```
3. Fully close your VM and VMware. Nothing VMware related (besides this guide of course) should be running.
4. Open File Explorer, click Documents, open the folder named Virtual Machines, open the folder with the name of your VM, and find your .vmx file (VMware virtual machine configuration). Open it with Notepad.
5. Scroll to the line `board-id.reflectHost = "FALSE"` and replace `FALSE` with `TRUE`.
6. Find the line `firmware = "efi"` and paste everything from the other Notepad window right below this.
7. Find the line `ethernet0.addressType = "generated"` and replace `generated` with `static`.&#x20;
8. Find the line `ethernet0.generatedAddress = "xx:xx:xx:xx:xx:xx"` and replace the entire line with `ethernet0.Address = "<YOUR CHOSEN MAC ADDRESS>"`
9.  Find the line `ethernet0.generatedAddressOffset = "0"` and replace the entire line with `ethernet0.checkMACAddress = "false"`.\


    {% hint style="warning" %}
    #### AMD USERS PLEASE READ

    Make sure that your VMX does not have any duplicate items from your previous modifications to make the VM start up! You may need to delete the following from the previous modifications:

    ```
    smbios.reflectHost = "TRUE"
    hw.model = "<HW MODEL FROM AMD GUIDE>" 
    board-id = "<BOARD ID FROM AMD GUIDE>"
    ```
    {% endhint %}


10. Save the .vmx file inside Notepad, boot it up, and try signing into your Apple ID from within the iMessage app. If all went well, you will be able to sign in and send messages.

## Error Messages

* You cannot sign into iMessage at this time ... contact Apple Support and provide the code below. This issue can be one of two things:
  * Go to System Preferences and go in iCloud, make sure you are logged in - if yes, proceed to the below:
  * You will need to do what the popup says and call Apple Support.
    1. You need to just say something like I just got this Mac from \<online reselling store in your country> and I can't sign in and it gives me a customer code
    2. Sometimes it takes a couple calls because the support person doesn't know what the customer code is
    3. It helps to have photos of the customer code
    4. Once you've got someone who knows what they're doing, it's a 5 minute process - they enter something server side, then you restart your Mac, and it works

## Clearing Failed Login Attempts

Run the following in terminal:

```
sudo rm -rf ~/Library/Caches/com.apple.iCloudHelper*
sudo rm -rf ~/Library/Caches/com.apple.Messages*
sudo rm -rf ~/Library/Caches/com.apple.imfoundation.IMRemoteURLConnectionAgent*
sudo rm -rf ~/Library/Preferences/com.apple.iChat*
sudo rm -rf ~/Library/Preferences/com.apple.icloud*
sudo rm -rf ~/Library/Preferences/com.apple.imagent*
sudo rm -rf ~/Library/Preferences/com.apple.imessage*
sudo rm -rf ~/Library/Preferences/com.apple.imservice*
sudo rm -rf ~/Library/Preferences/com.apple.ids.service*
sudo rm -rf ~/Library/Preferences/com.apple.madrid.plist*
sudo rm -rf ~/Library/Preferences/com.apple.imessage.bag.plist*
sudo rm -rf ~/Library/Preferences/com.apple.identityserviced*
sudo rm -rf ~/Library/Preferences/com.apple.ids.service*
sudo rm -rf ~/Library/Preferences/com.apple.security*
sudo rm -rf ~/Library/Messages
```

Reboot once done.
