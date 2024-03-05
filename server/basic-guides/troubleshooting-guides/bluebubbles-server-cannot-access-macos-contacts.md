---
description: >-
  The following guide will show you how you can manually modify the TCC database
  to allow BlueBubbles to access your macOS Contacts.
---

# Server Isn't Requesting for the Contacts Permission

## Why does this happen?

This will sometimes occur after disabling SIP (System Integrity Protection) on your Mac during the setup for the Private API. We are unsure exactly why this happens, but we know how to fix it.

## How can this be fixed?&#x20;

MacOS maintains a database of the allowed permissions. All we need to do is modify this database and manually add the BlueBubbles Server to the "allow" list for the Contacts permission.

**Technical Level of Effort:** Medium

## Steps to fix the issue

1. Open up the Terminal app on your Mac
   * Use the `CMD + Space` hotkey combo to open Spotlight Search and type `Terminal`
2. Run the following command to make a backup of the `TCC.db` file in case of corruption
   * `cp ~/Library/Application\ Support/com.apple.TCC/TCC.db ~/Library/Application\ Support/com.apple.TCC/TCC.db.bak`
3. Open the `TCC.db` database file using the following command:
   * `sqlite3 ~/Library/Application\ Support/com.apple.TCC/TCC.db`
   * Do not omit the `~` character from the file path
   * This will open the database for editing
4. Paste the following commands, hitting `ENTER` after each one. If you receive an error when running any of the following commands, do not worry, you can ignore them.
   * `INSERT INTO access VALUES('kTCCServiceAddressBook','com.BlueBubbles.BlueBubbles-Server',0,2,4,1,X'fade0c00000000b000000001000000060000000200000022636f6d2e426c7565427562626c65732e426c7565427562626c65732d5365727665720000000000060000000f000000060000000e000000010000000a2a864886f76364060206000000000000000000060000000e000000000000000a2a864886f7636406010d0000000000000000000b000000000000000a7375626a6563742e4f550000000000010000000a575056323735483857370000',NULL,0,'UNUSED',NULL,0,1675472593);`
   * `INSERT INTO access VALUES('kTCCServiceContactsFull','com.BlueBubbles.BlueBubbles-Server',0,2,4,1,X'fade0c00000000b000000001000000060000000200000022636f6d2e426c7565427562626c65732e426c7565427562626c65732d5365727665720000000000060000000f000000060000000e000000010000000a2a864886f76364060206000000000000000000060000000e000000000000000a2a864886f7636406010d0000000000000000000b000000000000000a7375626a6563742e4f550000000000010000000a575056323735483857370000',NULL,0,'UNUSED',NULL,0,1675472593);`
   * `INSERT INTO access VALUES('kTCCServiceContactsLimited','com.BlueBubbles.BlueBubbles-Server',0,2,4,1,X'fade0c00000000b000000001000000060000000200000022636f6d2e426c7565427562626c65732e426c7565427562626c65732d5365727665720000000000060000000f000000060000000e000000010000000a2a864886f76364060206000000000000000000060000000e000000000000000a2a864886f7636406010d0000000000000000000b000000000000000a7375626a6563742e4f550000000000010000000a575056323735483857370000',NULL,0,'UNUSED',NULL,0,1675472593);`
5. Once complete, type `.exit` and hit `ENTER` to leave the `sqlite3` CLI
6. Open the BlueBubbles Server (if not already open), and navigate to the Contacts tab
7. Use the `Permissions -> Refresh Permission Status` button in BlueBubbles see if the Contacts permission is authorized
   * If it is still not authorized, use the `Permissions -> Request Permission` button to try and force request the permission.
   * If it is still not authorized, you may need to manually enable the permission within the `Security & Privacy` settings within `System Preferences`

{% hint style="info" %}
If at any point, you mess up or feel like something went wrong, simply run the following command to replace the `TCC.db` file with the one you backed up at the start:

`mv ~/Library/Application\ Support/com.apple.TCC/TCC.db.bak ~/Library/Application\ Support/com.apple.TCC/TCC.db`

Now, you can restart the process from step 2
{% endhint %}

BlueBubbles should now be able to access your macOS Contacts!

## References

* [https://apple.stackexchange.com/questions/384310/how-do-i-configure-camera-and-microphone-permission-on-macos-mojave](https://apple.stackexchange.com/questions/384310/how-do-i-configure-camera-and-microphone-permission-on-macos-mojave)
* [https://www.rainforestqa.com/blog/macos-tcc-db-deep-dive](https://www.rainforestqa.com/blog/macos-tcc-db-deep-dive)

##
