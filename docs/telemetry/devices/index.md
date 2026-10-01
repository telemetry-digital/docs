---
title: Devices and data
slug: devices
sidebar_position: 3
tags: [devices, data, mqtt]
---

Devices send measurements to telemetry.digital, and telemetry.digital sends them configuration and commands. Every
measurement is stored as an **observation** that is never changed afterwards, with two timestamps (when it was
measured and when it arrived), its quality, and gaps where expected data is missing.

## Ways to connect

| Device | How | Page |
|---|---|---|
| Your own device or firmware (ESP32, a PLC gateway, a script) | MQTT over TLS or HTTP with a device token | [MQTT and HTTP devices](mqtt-http.md) |
| zigbee2mqtt, Tasmota, ESPHome, Shelly and other devices that announce themselves over MQTT | automatic device discovery | [Automatic device discovery](automatic-discovery.md) |
| PLCs and controllers with OPC UA or Modbus, LoRaWAN sensors through ChirpStack | a connector — the server polls them | [Connectors](connectors.md) |
| Many identical devices, devices with ThingsBoard firmware | a provisioning profile | [Fleets and provisioning](provisioning.md) |

All paths are shown with the actual host, ports and credentials under **Devices and connections → Add a device**.

## Working with devices

- [Commands, settings and firmware updates](commands-and-firmware.md) — commands with acknowledgement, shared
  attributes, the remote console and over-the-air firmware updates.

## Good to know

- **Reading and writing**: OPC UA and Modbus connectors write values too (for example set points on controllers).
  Every write is a command with your identity, a reason, an acknowledgement and an audit entry; with the four-eyes
  policy a second person approves it.
- **Nothing is lost silently**: every incoming message is kept as a raw message with the result of decoding, so a
  value with an unknown key or bad JSON is visible on the device page under *Messages*.
- **Client libraries**: C libraries for Arduino and ESP-IDF implement the device protocol (STM32 to follow). Nothing
  requires them — any device that speaks MQTT or HTTP works.

!!! danger "Not for safety functions"
    The server is not in the control loop. A command can be late, refused or lost. Emergency stops, interlocks and
    other safety functions are hard-wired, never built on telemetry.digital.
