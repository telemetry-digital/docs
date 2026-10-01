---
title: Fleets and provisioning
slug: devices-provisioning
sidebar_position: 4
tags: [devices, provisioning, thingsboard, fleet]
---

When you install tens or hundreds of identical devices, registering each one by hand is not practical. A
**provisioning profile** lets devices register themselves with a key and a secret.

## Create a profile

*Provisioning → New profile*. A profile has a key (`pk_…`) and a secret (shown once; *New secret* replaces it, and
devices already provisioned keep their tokens). Options:

- **Strategy**: create new devices, or accept only devices registered beforehand.
- **Approval**: a new device exists but its token is refused until someone approves it (*Provisioning → Devices
  waiting for approval*).
- A device-name pattern and a device limit.
- The site and room of new devices.
- **Automatic datastreams**: every new telemetry key creates a datastream under the device's asset (numbers become
  gauges, booleans states, texts and objects states with the text), at most 64 per device.

## How a device registers

Over **MQTT**: connect with the user name `provision`, publish to `/provision/request`:

```json
{"deviceName": "sensor-001", "provisionDeviceKey": "pk_…", "provisionDeviceSecret": "…"}
```

The answer arrives on `/provision/response` — only on the requesting connection — with the device's token. The device
then connects with the token as its user name.

Over **HTTP**: `POST /api/v1/provision` with the same body.

Failed attempts are limited to 10 per minute per address and per key and logged as security events. Successful ones
are not limited, so a fleet behind one NAT address registers at once.

## ThingsBoard firmware works unchanged

Devices with ThingsBoard firmware need no changes: telemetry and attributes on `v1/devices/me/telemetry` and
`v1/devices/me/attributes`, attribute requests, and RPC — a command arrives on `v1/devices/me/rpc/request/{n}` and
the device's answer on `v1/devices/me/rpc/response/{n}` acknowledges it. Each device receives only its own messages
although all use the same topic names. The provisioning messages above are the ThingsBoard ones, too.

## A single device

For one device, a **claim code** is simpler — see [MQTT and HTTP devices](mqtt-http.md).
