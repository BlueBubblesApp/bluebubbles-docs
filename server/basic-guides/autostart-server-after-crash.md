---
description: >-
  This document will guide you on how you can configure your Mac to
  automatically restart the BlueBubbles Server app if the app crashes (unsafe
  exit)
---

# Autostart Server After Crash

{% hint style="info" %}
Special thanks to [**Scr0nch**](https://github.com/Scr0nch) for the basis of this guide!
{% endhint %}

1. Open the TextEdit app on your Mac
2.  Copy & paste the following XML into the new text file:\
    \
    **Note: Modify the "Program" string value to the location of your BlueBubbles.app, and/or replace `{username}` with your macOS username**\


    <pre class="language-xml"><code class="lang-xml"><strong>&#x3C;?xml version="1.0" encoding="UTF-8"?>
    </strong>&#x3C;!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    &#x3C;plist version="1.0">
        &#x3C;dict>
            &#x3C;key>Label&#x3C;/key>
            &#x3C;string>app.bluebubbles&#x3C;/string>
            &#x3C;key>Program&#x3C;/key>
            &#x3C;string>/Users/{username}/Applications/BlueBubbles.app/Contents/MacOS/BlueBubbles&#x3C;/string>
            &#x3C;key>RunAtLoad&#x3C;/key>
            &#x3C;true/>
            &#x3C;key>KeepAlive&#x3C;/key>
            &#x3C;dict>
    	    &#x3C;key>SuccessfulExit&#x3C;/key>
    	    &#x3C;false/>
    	&#x3C;/dict>
        &#x3C;/dict>
    &#x3C;/plist>
    </code></pre>

    \
    This will make sure that the specified program will be run when you first login to your Mac (`RunAtLoad`), and will be restarted if it crashes (`KeepAlive`). The `SuccessfulExit: false` flag means that the app will _not_ be restarted if the server exits successfully; for example, if you manually close the app.
3. Save the file to `~/Library/LaunchAgents/app.bluebubbles.plist`
   1. `CMD + S` to save the file
   2. `CMD + Shift + G` to open a file location
   3. Paste `~/Library/` into the popup and hit Enter
   4. Find the `LaunchAgents` folder and open it
      * If it does not exist, create it using the `New Folder` button
   5. Enter `app.bluebubbles.plist` in the `Save As` field
4. Disable the built-in autostart option in the BlueBubbles Server:\
   ![](<../.gitbook/assets/image (9).png>)
5.  Install the launch agent and load it after the current user graphically logs in:

    ```
    launchctl bootstrap gui/$(id -u $(whoami)) ~/Library/LaunchAgents/app.bluebubbles.plist
    ```
6.  Immediately start the launch agent (only necessary for the first time):

    ```
    launchctl kickstart gui/$(id -u $(whoami))/app.bluebubbles
    ```

Some additional notes:

*   To uninstall the launch agent:

    ```
    launchctl remove app.bluebubbles
    ```
*   To print information about the launch agent:

    ```
    launchctl print gui/$(id -u $(whoami))/app.bluebubbles
    ```
