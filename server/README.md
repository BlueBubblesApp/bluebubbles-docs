---
description: Info on the BlueBubbles server app
---

# Server Overview

## Background

The server is primarily coded in TypeScript. Our first official stable release was published in August 2021 after extensive closed alpha and open beta testing since August 2020.

The server uses AppleScript to perform simple functions like sending messages & attachments and creating chats, and polls the `chat.db` database to see when new messages come in. We provide extra functionality in the form of the Private API bundle (docs linked in the sidebar), which uses native Objective-C to communicate with iMessage itself and access much deeper functions.

## Supported Mac Devices

Any macOS device running High Sierra and newer, with iMessage activated successfully.

{% hint style="info" %}
macOS El Capitan is _no longer supported_
{% endhint %}

macOS VMs are also compatible, however you **must** be able to use iMessage (this involves some work with spoofing your hardware to seem like a real Mac inside the VMX file). See [running-a-macos-vm](advanced/macos-virtualization/running-a-macos-vm/ "mention") for more details.

If you'd like the most stable experience, we recommend going with Catalina or Big Sur.

## Downloads

{% @github-files/github-code-block url="https://github.com/BlueBubblesApp/bluebubbles-server/releases" %}

## Contributors <a href="#contributors" id="contributors"></a>

### Main Developers <a href="#main-developers" id="main-developers"></a>

{% @github-files/github-code-block url="https://github.com/zlshames" %}

### Other Contributors <a href="#other-contributors" id="other-contributors"></a>

We appreciate y'all!

{% @github-files/github-code-block url="https://github.com/elliotnash" %}
