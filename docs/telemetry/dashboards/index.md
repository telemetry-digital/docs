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

![A dashboard with a table of latest values, a list of devices with their state and a list of active alarms with severity](img/overview.webp)

*A dashboard with latest values, devices and alarms.*

![A dashboard on a phone: the widgets stacked in one column](img/phone-dashboard.webp)

*On a phone the widgets are stacked in one column.*

## Pages in this section

1. [Dashboard widgets](widgets.md) — the widget catalogue, settings, filters, public links.
2. [Process screens (SCADA)](scada.md) — the editor, symbols, dynamics, faceplates, SVG import.
3. [Displays (kiosks)](displays.md) — screens at a wall that show content in turn without anyone signed in.
4. [Period tables](period-tables.md) — daily and monthly tables with Excel, CSV and PDF export.

Video walls and cameras on a floor plan are described with the [Video NVR](../video-nvr/video-walls.md).

## Versions and reasons

Dashboards and process screens are **versioned**: every save is a new version with a reason, written to the audit
trail. Creating and changing them needs the permission to edit content; viewing needs only read access.

![The Dashboards page: a table of dashboards and process pictures with the number of widgets, version, default flag and creation time, and the buttons Open and Edit](img/dashboards.webp)

**New dashboard** asks for the name, the kind and start (a dashboard or a process picture, empty or from an example),
whether it is the default screen on the home page, and a reason.

![The New dashboard dialog with name, kind and start, the option Show on the home page (default) and a reason](img/new-dashboard.webp)

## Maps

Devices with a position (from their heartbeat) can be shown on a map, with your organization's map tiles or a
self-hosted map archive (PMTiles), so maps work without any external service.
