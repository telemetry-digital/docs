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

## Shared attributes (configuration)

Shared attributes are the device's configuration kept on the server — for example a reporting interval or a limit.
When someone changes them, the server republishes the retained document on `d/{id}/attributes/shared`, so a device
that reconnects always gets the current configuration. The device reports its own state (firmware, hardware, IP) as
client attributes, shown next to the shared ones on the device page.

## Remote console

The device page can open a **remote console** — a text channel into the device's firmware (for example
`wifi status`). It is never a shell into the server. The device answers line by line; everything is logged.

## Firmware updates over the air (FOTA)

1. **Firmware → Upload**: the image, its version and hardware id. Images can be signed by their author (Ed25519).
2. Create a **campaign** for devices, a group or a profile, with **staged rollout** — a few devices first, then a
   percentage, then all.
3. Each device receives the target, downloads the image with its token (resumable), verifies the SHA-256 and the
   signature, installs it into its second slot and reports its progress: *downloading*, *verifying*, *applying*,
   *updated* — or *failed* / *rolled back*. A device that does not confirm the new version in time rolls back.

Every campaign and its changes are in the audit trail.

!!! note "Device side"
    FOTA, the console and command handling need support in the device's firmware. The client libraries for Arduino
    and ESP-IDF implement them; other devices follow the device protocol.
