---
description: This guide helps you run BlueBubbles for multiple people on the same Mac
---

# Multiple Users on the Same Mac

## Instructions

1. Create another user account on your Mac, preferably with admin capabilities
2. Turn on ["Fast Login Switching"](https://support.apple.com/en-tm/guide/mac-help/mchlp2439/mac)
   * When you switch between accounts on the Mac, you will want to use the **Switch accounts using the menu bar** method
3. Setup BlueBubbles on the new user
   1. You will need to provide a _**different**_ Firebase JSON and Admin SDK JSON for this user
   2. You will need to provide a _**different**_ Ngrok auth token for this user
   3. You will need to provide a different port number for the server to use

The other user should now be able to use BlueBubbles simultaneously with the original BlueBubbles user!

### Caveats / Notes

* You **will not** be able to auto-login multiple users. You can only select one user to be auto logged in when macOS first boots up, or after a power outage.
