---
title: Dashboards and SCADA
slug: dashboards
sidebar_position: 4
tags: [dashboards, scada, visualisation]
---

There are three ways to show your data, and you can mix them on one screen:

- **Dashboards** of widgets — values, gauges, charts, tables, controls. Quick to build, good on phones.
- **Process screens (SCADA)** — the plant drawn in a vector editor with industrial symbols, pipes with flow, live
  values and faceplates with commands.
- **Both on one screen** — a process screen with widgets placed over the drawing.

Values update live, without reloading the page.

## Pages in this section

1. [Dashboard widgets](widgets.md) — the widget catalogue, settings, filters, public links.
2. [Process screens (SCADA)](scada.md) — the editor, symbols, dynamics, faceplates, SVG import.
3. [Displays (kiosks)](displays.md) — screens at a wall that show content in turn without anyone signed in.
4. [Period tables](period-tables.md) — daily and monthly tables with Excel, CSV and PDF export.

Video walls and cameras on a floor plan are described with the [Video NVR](../video-nvr/video-walls.md).

## Versions and reasons

Dashboards and process screens are **versioned**: every save is a new version with a reason, written to the audit
trail. Creating and changing them needs the permission to edit content; viewing needs only read access.

## Maps

Devices with a position (from their heartbeat) can be shown on a map, with your organization's map tiles or a
self-hosted map archive (PMTiles), so maps work without any external service.
