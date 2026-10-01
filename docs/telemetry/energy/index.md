---
title: Energy
slug: energy
sidebar_position: 5
tags: [energy, meters, consumption]
---

The **Energy** page shows the consumption of all your energy meters in one place — per hour, per day and per month,
with the cost at your price per kWh, the current power and each meter's share.

## Which meters count

- The energy sensors of devices recognized by [automatic device discovery](../devices/automatic-discovery.md) —
  Shelly, Tasmota, zigbee2mqtt and others.
- **Every datastream in Wh, kWh or MWh** — from your own devices, Modbus or OPC UA meters, or LoRaWAN sensors.

Meters are cumulative counters: consumption is the **sum of increases** between readings. A meter that drops below
half its previous reading is taken as reset, and its new reading counts.

## What the page shows

- Consumption per **hour** (one day), per **day** (a week or a month) or per **month** (a year), in your
  organization's time zone.
- The **cost** at your price per kWh.
- The **current power** — the sum of all power sensors.
- Each meter's **share** of the total.
- **Production** (for example solar panels) in green, with the price paid for produced energy.

## Settings

*Energy → Settings*:

- **Price per kWh** and the currency; the price paid for produced energy.
- Each meter's **role**: consumption, production, or not counted.
- A device with several energy sensors (Tasmota reports Total, Today and Yesterday) counts only the one named
  *total* unless you choose otherwise.

## More than the overview

- Monthly consumption of each meter, meter readings for accounting and the hourly load of a day are
  [period tables](../dashboards/period-tables.md) with Excel, CSV and PDF export.
- Charts and gauges of power and consumption go on any [dashboard](../dashboards/widgets.md).
- Alarm rules and flows react to consumption like to any other value — see [Automation](../automation/index.md).
- [PDF reports and exports](pdf-and-exports.md) turn the data into documents and files.
