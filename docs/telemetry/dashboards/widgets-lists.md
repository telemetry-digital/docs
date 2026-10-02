---
title: List widgets
slug: dashboard-widgets-lists
sidebar_position: 5
tags: [dashboards, widgets, tables, alarms, reference]
---

List widgets show values, alarms and devices as tables. The tables list the settings on the **Data** tab; the shared
Appearance, Series and Actions settings are described in the [widget catalogue](widgets.md). The period table and
the overview table offer an export; tables that do not fit their widget scroll inside it. The values table and the
entity table can also select their datastreams by a query (up to 500) — see
[Filters and overviews](filters-and-overviews.md).

## Values table

The latest value, unit, time and quality of each bound datastream.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Values table | — |
| Decimals | number | 2 | any; a series can set 0–6 |

| Column | Content |
|---|---|
| Asset | the asset of the datastream |
| Datastream | the series label or the datastream key |
| Value | the latest value |
| Unit | the unit (series unit or datastream unit) |
| Measured | the time of the reading |
| Quality | a green *ok* badge, or an amber badge with the quality |

**Without data**: `—` in Value and Measured and an empty Quality.

## Entity table

The latest values of many datastreams with a search box and sorting — an overview of a whole plant or building.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Entity table | — |
| Decimals | number | 2 | any |
| Show time | checkbox | on | the column Measured |
| Show quality | checkbox | on | the column Quality |
| Search box | checkbox | on | — |

- A click on **Datastream**, **Value** or **Measured** sorts by it; another click reverses the order (▲ ▼).
- The search box keeps the rows whose name contains the text; the sorting and the search stay while the widget
  refreshes.
- Values take the colour of a matching conditional-formatting rule or the *Value colour*.
- **Without data**: `—`; when the search matches nothing, *nothing found*.

## Overview table

Many datastreams in one table — usually selected by a query and following the dashboard filters — with a search box,
sorting, column filters (value range, quality, stale or fresh, alarm state), grouping by site, asset, asset type,
quantity or device with totals, pages, highlighting of alarms and values outside the alarm-rule limits, and Excel and
CSV export of the filtered rows. Described in [Filters and overviews](filters-and-overviews.md#the-overview-table).

## Time-series table

One row per time stamp and one column per bound datastream — the readings themselves, newest first.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Time-series table | — |
| Range (empty = dashboard range) | list | (default) | 1h, 6h, 8h, 12h, today, 24h, 7d, 30d, 90d |
| Decimals | number | 2 | any |
| Rows | number | 50 | 1–1000 |

- The **Time** tab sets a fixed window, the aggregation and the interval — see [time window](widgets.md). With
  aggregation *none* the table lists the raw readings (time stamps rounded to whole seconds); with an aggregation each
  row is a bucket, so values of several datastreams line up in one row.
- Column headers are the series labels (or keys) with the unit in brackets.
- Conditional formatting colours individual cells.
- **Without data**: *no data in this range*.

## Period table

Values per local hour, day, week, month or year — minimum and maximum, average, start and end readings, consumption,
number of readings or readings out of limits — with Excel, CSV and PDF export. Described in
[Period tables](period-tables.md).

## Alarms

The recent alarms of the organization, active ones first.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Alarms | — |
| Rows | number | 10 | any whole number |

| Column | Content |
|---|---|
| Raised | when the alarm was raised |
| Severity | a red *action* or an amber *warning* badge |
| State | the alarm state, with *· cleared* for cleared alarms |
| Key | the datastream key |
| Value | the value that raised the alarm, with 2 decimals |
| (button) | **Acknowledge** on an active alarm, for users with `alarm.ack` |

- The header line counts the active alarms (*3 active*).
- **Acknowledge** asks for a reason, stops the alarm's escalation and is written to the alarm log and the audit
  trail (see [Alarms](../automation/alarms.md)). The button is never shown on a public link.
- The list updates when an alarm of the organization changes.
- On a public link the list contains only alarms of the datastreams bound by the dashboard's widgets.
- **Without data**: *no alarms*.

## Devices

The connection state of the devices of the organization, or of the selected ones.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Devices | — |
| Devices (empty = all) | devices | all | up to 24 devices |
| Rows | number | 20 | any whole number |

| Column | Content |
|---|---|
| Device | the device name (or its external id), a link to the device page |
| Transport | how the device connects: mqtt, http, lorawan, opcua or modbus |
| State | see below |
| Last seen | the time of the last contact |

| State | Colour | Meaning |
|---|:---:|---|
| online | green | the device is connected now |
| recent | amber | not connected, seen within the last hour |
| offline | red | not seen for more than an hour |
| never | grey | the device has never reported |

Revoked devices are not listed. The list updates when a device connects or disconnects. On a public link the device
names are not links. **Without data**: *no devices*.
