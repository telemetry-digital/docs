---
title: Period tables
slug: period-tables
sidebar_position: 4
tags: [dashboards, reports, export, xlsx, pdf]
---

The **Period table** widget shows datastream values per local period — hour, day, week, month or year in your
organization's time zone — and exports exactly that table to **Excel (XLSX)**, **CSV** and a **PDF report**.

## Typical set-ups

| Need | Datastreams | Row per | Values | Period | Other |
|---|---|---|---|---|---|
| Daily temperature log of a medicine fridge | the fridge sensor | day | min and max | this month | lower limit 2, upper limit 8, total row |
| Monthly consumption of electricity meters | all meters (kWh counters) | month | consumption | last 12 months or this year | a row per datastream, total row |
| Meter readings for accounting | the meters | month | start, end and consumption | last month | a row per datastream |
| Hourly load of one day | a meter | hour | consumption | today | — |

Add it with *Add widget → Period table*. The names in the table come from the widget's *Series* tab (for example
"Main meter", "Heating").

## What the values mean

| Value | Meaning |
|---|---|
| min, max, average | of the samples in the period |
| start, end | the first and the last reading in the period |
| consumption | the sum of increases of a counter; a meter reset counts the new reading; the last reading before the period is the baseline, so consecutive periods add up |
| samples | the number of readings |
| out of limits | readings below the lower or above the upper limit |

Periods start at local midnight and on the local 1st of the month, also on the days the clocks change (23 or 25
hours). Days without data stay empty. Values outside the limits are red; a `*` marks a period with readings of doubtful
quality. ◀ ▶ move to the previous or next period, *Current* returns; the export takes the period shown.

## Export

Excel, CSV and PDF buttons appear for users with the permission `data.export` (not on public links). The file carries
the title, the period, the time zone, the organization, the author and the time; headers are in the user's language.
PDF reports are stored under *Settings → Stored reports* with their hashes.

Every export is written to the audit trail.

!!! note "Records for inspections"
    A daily temperature log is often a regulated record. telemetry.digital stores the measurements append-only and
    audits every export, but it is not a validated system by itself — the validation of your use stays with you.
