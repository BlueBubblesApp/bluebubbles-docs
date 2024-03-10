---
description: >-
  This document will guide you on how to fix issues with sending messages to
  international phone numbers.
---

# Unable to Send Messages to International Contacts

## Why are my messages not sending?

Typically, when you are having issues sending messages to international phone numbers (non-US), it is due to a missing region code. When the BlueBubbles Server receives a request to send a message, it will do the following:

1. Checks if the phone number has a region code (i.e. `+1` for US numbers)
   * If it does, it will be used to send the message (best case)
2. If no region code is provided, the BlueBubbles Server will use the region code set by the base macOS system
   * This is the next best option, but not always a sure-fire solution
3. If no region code is provided and the BlueBubbles Server cannot determine the macOS region, `+1` will automatically be applied to the phone number
   * **This is likely what is occurring, and why messages are not sent properly**

## How can I fix the issue?

## Best Solution

The best solution to this issue is to make sure that your Contacts on your Android device includes the region code for the country the person is from.

For instance, if the person is from the United Kingdom, make sure the phone number associated with their Contact entry is something like: `+44 20 7123 4567`

### Fallback Solution

The next best option is to set your macOS device's region to the region that the majority of your Contacts reside in.

To do this, open `System Preferences` and navigate to the `Date & Time` settings.

<figure><img src="../.gitbook/assets/Screenshot 2024-03-10 100425.png" alt=""><figcaption></figcaption></figure>

Find the `Region` drop-down, and select your default region

<figure><img src="../.gitbook/assets/Screenshot 2024-03-10 100705.png" alt=""><figcaption></figcaption></figure>

Once you've set it, close System Preferences and then restart the BlueBubbles Server. That's it! The server should now "imply" your default region code if no region code is provided with each phone number.

{% hint style="info" %}
We still highly advise making sure that your Contacts on your Android device already include a region code.
{% endhint %}
