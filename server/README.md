---
description: Info on the BlueBubbles server app
---

# Intro and Background

## Background

The server is primarily coded in TypeScript. Our first official stable release was published in August 2021 after extensive closed alpha and open beta testing since August 2020.

The server uses AppleScript to perform simple functions like sending messages & attachments and creating chats, and polls the `chat.db` database to see when new messages come in. We provide extra functionality in the form of the Private API bundle (docs linked in the sidebar), which uses native Objective-C to communicate with iMessage itself and access much deeper functions.

## Supported Mac Devices

Any macOS device running El Capitan and higher, with iMessage activated successfully.

macOS VMs are also compatible, however you **must** be able to use iMessage (this involves some work with spoofing your hardware to seem like a real Mac inside the VMX file). See [running-a-macos-vm](advanced/macos-virtualization/running-a-macos-vm/ "mention") for more details.

If you'd like the most stable experience, we recommend going with Catalina or Big Sur.

## Downloads

{% embed url="https://github.com/BlueBubblesApp/bluebubbles-server/releases" %}
GitHub Releases (Stable and Alpha)
{% endembed %}

## Contributors <a href="#contributors" id="contributors"></a>

### Main Developers <a href="#main-developers" id="main-developers"></a>

{% embed url="https://github.com/zlshames" %}
Zach
{% endembed %}

### Other Contributors <a href="#other-contributors" id="other-contributors"></a>

We appreciate y'all!

{% embed url="https://github.com/elliotnash" %}
Elliot
{% endembed %}
