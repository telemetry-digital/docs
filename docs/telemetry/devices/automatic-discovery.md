---
title: Automatic device discovery
slug: devices-discovery
sidebar_position: 4
tags: [devices, mqtt, smart-home, discovery]
---

Devices that announce themselves over MQTT are **recognized automatically** — with their rooms and entities —
recorded, controlled from the **Home** page and available in flows. Everything runs inside the server: the MQTT broker,
the automatic device discovery and the bridges. No other home-automation software, Mosquitto or Node-RED is needed
(an existing installation can keep running next to it).

Supported: zigbee2mqtt, Tasmota, ESPHome, Z-Wave JS UI, OpenMQTTGateway, Shelly Gen2 and later, and any device that
publishes the common MQTT discovery format.

## Devices and connections

**Devices and connections** (from the **Home** page) needs the permission `device.manage` and has four tabs:

| Tab | Purpose |
|---|---|
| Add a device | guided paths for each kind of device |
| Discovered devices | the inbox of devices waiting to be adopted; a badge shows how many wait |
| MQTT accounts and bridges | the MQTT accounts of this server and the bridges to other brokers |
| Provisioning | provisioning profiles and devices waiting for approval (see [Fleets and provisioning](provisioning.md)) |

![Devices and connections → Add a device: cards for a smart-home device, an existing MQTT broker, a fleet of devices, a single device and an industrial connector, and the tabs Discovered devices, MQTT accounts and bridges, and Provisioning](img/add-a-device.webp)

**Add a device** offers five paths:

| Path | What it shows |
|---|---|
| Smart-home device | choose an MQTT account (or create one) and get ready-to-paste settings for zigbee2mqtt, Tasmota, ESPHome and any other device, each with a **Copy** button |
| Existing MQTT broker | create a bridge to your broker |
| Fleet of devices | choose a provisioning profile and get the MQTT and HTTP provisioning requests |
| A single ctrl32 device | opens **Devices** to register one device with a claim code |
| Industrial connector | opens **Settings → Connectors** (OPC UA, Modbus TCP, ChirpStack) |

## MQTT accounts

An **MQTT account** lets devices connect to the broker built into this server. Its devices use whatever topics they
like inside a **private namespace**: two accounts never see each other's messages.

![The tab MQTT accounts and bridges: two MQTT accounts with their generated user names, connection state, number of entities and new devices, the buttons Edit, New password and Delete, and the buttons New MQTT account and New bridge](img/mqtt-accounts.webp)

**New MQTT account** generates a user name (`hs_…`) and a password. Both are shown **once** in a dialog with **Copy**
buttons — save them now. The server keeps only a hash of the password.

| Field | Default | Limits | Meaning |
|---|:---:|:---:|---|
| Name | — | required, at most 128 characters, unique | for example the apartment or building |
| Discovery prefix | `homeassistant` | a topic without `#` or `+`, at most 64 characters | the topic prefix under which devices publish their discovery messages |
| New devices | add automatically | add automatically, ask first (inbox) | whether a newly announced device is created at once or waits in **Discovered devices** |
| Incident when disconnected longer than (minutes, 0 = never) | 10 | 0, or 0.1–10 080 | an *MQTT connection* incident opens when an account whose devices have entities stays disconnected that long |
| Reason (audit trail) | — | required | — |

The table shows for each account its name and user name, **Connection** (connected or not connected, with the last
error), the number of **Entities** and of **New devices** waiting (a link to the inbox), and the buttons:

- **Edit** — the fields above.
- **New password** — issues a new password (shown once) and **disconnects** all clients of the account; asks for a
  reason.
- **Delete** — revokes the account, disconnects its clients and marks its entities as removed; asks for a reason.

Ready-to-paste settings (the *Smart-home device* path fills in your host, port, user and prefix):

- **zigbee2mqtt** (`configuration.yaml`): `mqtt.server`, `mqtt.user`, `mqtt.password`, and
  `homeassistant.enabled: true` with `discovery_topic` (that is zigbee2mqtt's own name for publishing discovery
  messages).
- **Tasmota** (console): `Backlog MqttHost …; MqttPort …; MqttUser …; MqttPassword …; SetOption19 1`. Without
  `SetOption19 1` Tasmota's own discovery is understood natively.
- **ESPHome** (YAML): `mqtt:` with `broker`, `port`, `username`, `password`, `discovery: true` and
  `discovery_prefix`.
- **Shelly Gen2 and later** (the device's web page, *Settings → MQTT*): enable, server `host:port`, the account's
  user and password, keep *RPC status notifications* on. Switches, covers, lights (also RGB/RGBW), inputs and power,
  energy, voltage, current, temperature, humidity and battery values are added.

## Bridges to an existing broker

A **bridge** connects the server to your existing broker (for example the Mosquitto of zigbee2mqtt) as a client and
takes the devices that announce themselves there. Your existing programs keep working as before.

| Field | Default | Limits | Meaning |
|---|:---:|:---:|---|
| Name | — | required, at most 128 characters, unique | — |
| Broker host | — | required, host name or address, at most 255 characters, no `/` or spaces | your broker |
| Port | 1883 | 1–65535 | — |
| TLS | off | — | connect with TLS |
| Accept a self-signed certificate | off | — | skip the certificate check (only for a broker you control) |
| User name | — | — | for the broker |
| Password | — | empty keeps the stored one | stored encrypted |
| Client id | generated | — | the MQTT client id of the bridge |
| Publish these values on the broker | none | at most 200 datastreams | the selected datastreams are published to the broker together with discovery messages, so another system on that broker (for example an existing Home Assistant) shows them as sensors |
| Discovery prefix | `homeassistant` | as for accounts | — |
| New devices | ask first (inbox) | add automatically, ask first (inbox) | — |
| Incident when disconnected longer than (minutes, 0 = never) | 10 | 0, or 0.1–10 080 | an *MQTT connection* incident opens and administrators are notified |
| Reason (audit trail) | — | required | — |

The connection is re-established automatically; its state and last error are shown in the table. **Delete** stops
the bridge and marks its entities as removed.

## Discovered devices (the inbox)

![The tab Discovered devices: a Shelly Plus1PM named Boiler with 7 entities waiting, and the buttons Adopt and Ignore](img/discovered-devices.webp)

Devices that announced themselves through a bridge — or through an account set to *ask first* — wait here.

| Column | Content |
|---|---|
| Device | the announced name and the device key |
| Model | manufacturer and model |
| Entities | the number of entities and their kinds (sensor, switch, …) |
| Connection | the account or bridge it came through |
| Seen | when it last announced itself |
| Actions | **Adopt** and **Ignore** for a waiting device; otherwise its status |

- **Adopt** creates the device (transport `mqtt_discovery`) with its entities; asks for a reason.
- **Ignore** leaves the device out; asks for a reason.
- **Also show adopted and ignored devices** lists them too, with the status *adopted* or *ignored*.

At most 500 entries are listed. Adopting needs the account or bridge to be active.

## Entities

A discovered device appears in the room of its suggested area, with one **entity** per announced function: sensor,
binary sensor, switch, light, cover, valve, fan, lock, climate, number, select, text, button, scene, siren,
humidifier, event, device tracker, update.

Every state is recorded as a measurement — numbers every report, other states when they change — so charts,
reports, alarm rules and flows work with them like with any telemetry. Renaming an entity keeps your name even when
the device announces itself again. Renaming or moving an entity needs `device.manage`; switching it (a command)
needs `device.command` and follows the same four-eyes policy and audit as any command.

Next: [Home page and scenes](../automation/home-and-scenes.md) to control the devices, and
[Energy](../energy/index.md) for their consumption.

!!! note "Not yet supported"
    Shelly Gen1 (`shellies/…`), camera and image entities, and light effects on the cards.
