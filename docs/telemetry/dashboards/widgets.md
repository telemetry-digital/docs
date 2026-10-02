---
title: Dashboard widgets
slug: dashboard-widgets
sidebar_position: 1
tags: [dashboards, widgets]
---

A dashboard is a grid of widgets. Create one under **Dashboards → New**, then **Add widget** — the catalogue lists
more than 40 widget types grouped as Charts, Values, Lists and Other, each with a small drawing and a description.

![The Add widget dialog listing widget types in groups such as Process symbols and Charts, each with a drawing and a description](img/add-widget.webp)

## The catalogue

| Kind | Examples |
|---|---|
| Values | value card with icon and trend arrow, aggregated value (min, max, average, sum over the range), progress bar, liquid level, thermometer, battery level, signal strength, compass / wind direction, radial and linear gauges with coloured bands, LED and indicator grid, status |
| Charts | time series with two axes, bars over time (grouped or stacked), state timeline, pie and polar area, radar |
| Lists | time-series table, entity table (latest values of many datastreams with sort and search), alarm count, [period table](period-tables.md) |
| Controls | knob, slider, round switch — each sends a command with a reason |
| Other | clock, Markdown text with value placeholders, image, QR code, navigation to another screen, [camera](../video-nvr/video-walls.md) |

Start from the example *Dashboard — showcase (all widget types)* to see every type with demo data.

![The showcase dashboard with every widget type: value cards, gauges, trends, a heatmap, a map, tables, alarms, bars, pie, polar area, radar, a clock, Markdown, a QR code, a knob, a slider and a switch](img/showcase.webp)

*The showcase dashboard.*

## Widget settings

Each widget has tabs:

- **Data** — the datastreams it shows.
- **Appearance** — background, title, colours, ranges and coloured bands, conditional formatting.
- **Series** — per-series options and names, left or right axis.
- **Time** — the time window and aggregation (for example maximum per hour over 7 days). The ranges *8 h*, *12 h*
  (a shift) and *today* (since local midnight) are there for production screens.
- **Actions** — what a click does, for example open another screen.

![Editing a dashboard: a selected widget outlined in red and the properties panel on the right with the tabs Data, Appearance, Series and Action, the datastream list, title, decimals and limits](img/widget-properties.webp)

*In edit mode a click on a widget opens its properties on the right.*

The preview changes immediately; save with a reason. Widgets can be opened full screen, and some offer **filters** the
viewer can switch directly on the widget.

## Control widgets

Knobs, sliders and switches send **commands** to devices — including writes to OPC UA and Modbus. Each command needs
the permission `device.command` and a reason, is acknowledged by the device and audited; with the four-eyes policy a
second person approves it. Viewers without the permission do not see the controls. See
[Commands](../devices/commands-and-firmware.md).

## Public read-only links

A dashboard can be **shared** through a public read-only link with a QR code — for example for a screen in a
reception. Public links show values but never send commands, never navigate elsewhere, never offer exports, and
**never show cameras**.

The link is created under *Dashboard settings → Public link* with a reason; regenerating it invalidates the old link.
The same dialog sets the name, the default range, the refresh interval, the row height and whether the dashboard is
the default on the home page.

![The Dashboard settings dialog with name, default range, refresh, row height, Show on the home page and the Public link section with a reason and the button Create public link](img/dashboard-settings.webp)
