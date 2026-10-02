---
title: Commands, settings and firmware updates
slug: devices-commands-firmware
sidebar_position: 5
tags: [devices, commands, fota, firmware, console]
---

## Commands with acknowledgement

A command goes to the device on `d/{id}/cmd` (or is picked up by an HTTP device with `GET /api/v1/d/cmd?wait=30`).
The device answers with the same id on `d/{id}/cmd/ack`. The command's status follows it: *queued*, *sent*,
*acknowledged* or *failed* — or *expired* when the device does not answer in time.

- Sending a command needs the permission `device.command` and a reason.
- With the organization's **four-eyes policy** (*Settings → Security policy*) every command waits as *pending* until a
  different user with `device.command` approves it; the device sees only approved commands.
- Every command, approval and rejection is in the audit trail.

The *Commands* tab of a device shows the command log — method, arguments, status, result and who sent it — and a form
to send a command with a method, JSON arguments, a time to live (TTL) and a reason.

![The Commands tab of a device: a command log with a reboot command marked sent, and the form Send command with method, TTL, JSON arguments and a reason](img/device-commands.webp)

## Shared attributes (configuration)

Shared attributes are the device's configuration kept on the server — for example a reporting interval or a limit.
When someone changes them, the server republishes the retained document on `d/{id}/attributes/shared`, so a device
that reconnects always gets the current configuration. The device reports its own state (firmware, hardware, IP) as
client attributes, shown next to the shared ones on the device page.

![The Attributes tab of a device: the shared attributes, a field to set attributes as a JSON object, a field to remove keys, a reason and the Publish button, and the client attributes reported by the device](img/device-attributes.webp)

## Remote console

The device page can open a **remote console** — a text channel into the device's firmware (for example
`wifi status`). It is never a shell into the server. The device answers line by line; everything is logged.

![The Console tab of a device with the button Open console and the note that sessions close after 15 minutes without activity](img/device-console.webp)

*The device listens only while a session is open; it closes after 15 minutes without activity.*

## Firmware updates over the air (FOTA)

1. **Firmware → Upload**: the image, its version and hardware id. Images can be signed by their author (Ed25519).

    ![The Upload firmware image dialog with name, version, hardware id, minimum current version, Ed25519 signature, file, notes and a reason](img/firmware-upload.webp)

    ![Firmware → Images: an image with its name, version, hardware, size, SHA-256, upload time and number of campaigns](img/firmware.webp)

2. Create a **campaign** for devices, a group or a profile, with **staged rollout** — a few devices first, then a
   percentage, then all.

    ![Firmware → Campaigns: a campaign with its firmware, the status pending and a Cancel button](img/firmware-campaigns.webp)

3. Each device receives the target, downloads the image with its token (resumable), verifies the SHA-256 and the
   signature, installs it into its second slot and reports its progress: *downloading*, *verifying*, *applying*,
   *updated* — or *failed* / *rolled back*. A device that does not confirm the new version in time rolls back.

Every campaign and its changes are in the audit trail.

!!! note "Device side"
    FOTA, the console and command handling need support in the device's firmware. The client libraries for Arduino
    and ESP-IDF implement them; other devices follow the device protocol.
