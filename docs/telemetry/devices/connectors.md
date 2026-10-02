---
title: Connectors (OPC UA, Modbus, LoRaWAN)
slug: devices-connectors
sidebar_position: 3
tags: [devices, opc-ua, modbus, lorawan, plc]
---

A **connector** is the server reaching out to a device that does not send data on its own: a PLC with an OPC UA
server, a controller with Modbus, or a LoRaWAN network server. Connectors are set up under **Settings → Connectors →
New connector**. Each one appears as a device, so datastreams, alarms, gaps, dashboards and reports work exactly as
with MQTT devices.

![Settings → Connectors: a list of connectors with their kind, ingest address, messages, last contact and state, and the section Register profiles for Modbus](img/connectors.webp)

![The New connector dialog with name, kind ChirpStack HTTP integration, the option Auto-create devices by DevEUI and a reason](img/new-connector.webp)

## OPC UA client

The server reads nodes of an OPC UA server every N seconds — and writes them.

- **Endpoint**, **security** (from None to Basic256Sha256 with Sign or Sign and Encrypt, using the server's own client
  certificate, which you can download and trust on the PLC) and **sign-in** (anonymous or user name and password).
- **Nodes** as rows `node id | key | scale | offset | rw | min | max`. A node marked `rw` with limits can be written.
- **Test connection** connects, reads the nodes and disconnects — try it before saving.
- Nodes that cannot be read are reported, not fatal; the connection is retried with a back-off.

!!! note "The server must reach the PLC"
    With OPC UA the server is the client. A server in a data centre cannot reach a PLC behind a NAT — install the
    server at the site (see [Cloudflare Tunnel](../getting-started/cloudflare-tunnel.md)) or connect the site with
    WireGuard.

## Modbus TCP client

The server polls registers of a Modbus TCP device, or of RTU devices behind a Modbus TCP gateway (RTU over TCP).

- **Host, port, unit id, interval, timeout, byte order** (big, big swapped, little, little swapped).
- **Registers** as rows `key | table | address | type | scale | offset | rw | min | max` — tables holding, input,
  coil and discrete; types bool, int16, uint16, int32, uint32, float32, int64, uint64, float64. Register profiles are
  versioned.
- **Test connection** reads all registers once.
- Holding registers and coils marked `rw` can be written.

## LoRaWAN through ChirpStack

LoRaWAN sensors come in through a **ChirpStack** network server:

1. Create a connector of the kind ChirpStack. The server shows an **ingest address** and a **bearer secret** — the
   secret is shown once.
2. In ChirpStack add an **HTTP integration** with that address and the secret.
3. Uplinks are decoded per device profile and stored like MQTT telemetry. With *register unknown DevEUIs
   automatically* new sensors appear as devices by themselves.

## Writing values

Writing to an OPC UA node or a Modbus register is a **command**:

- it needs the permission `device.command` and a reason;
- the value is checked against the type and the min/max limits **before** anything is written;
- after the write the server reads the value back, and the command is *acknowledged* with the read-back value or
  *failed* with the error;
- everything is in the audit trail, and with the four-eyes policy a second person approves the write first.

Writes can come from dashboard control widgets (knob, slider, switch), from process screens, from flows and from the
device page.

!!! danger "Set points, not safety"
    Use writes to change set points and modes. Never rely on them for anything safety-related: the server is not in
    the control loop and a write can be refused or fail.
