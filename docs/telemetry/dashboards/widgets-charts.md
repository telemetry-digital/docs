---
title: Chart widgets
slug: dashboard-widgets-charts
sidebar_position: 4
tags: [dashboards, widgets, charts, trends, reference]
---

Chart widgets draw datastreams **over time** (trend chart, bars over time, state timeline, heatmap) or **compare the
latest values** of several datastreams (compare values, share, pie, polar area, radar). The tables list the settings on
the **Data** tab; the shared Appearance, Series and Actions settings are described in the
[widget catalogue](widgets.md). Chart widgets send no commands and offer no export; for tables with export use the
[period table](period-tables.md).

## Trend chart

One or more datastreams over time, as lines, areas, steps, bars or points, with up to two value axes.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Trend chart | — |
| Style | list | line | line, area, step, bar, scatter |
| Range (empty = dashboard range) | list | (default) | 1h, 6h, 8h, 12h, today, 24h, 7d, 30d, 90d |
| Decimals | number | 2 | any |
| Axis minimum (empty = auto) | number | — | any |
| Axis maximum (empty = auto) | number | — | any |
| Warning limit (high) | number | — | any |
| Action limit (high) | number | — | any |
| Show min–max band | checkbox | on | — |
| Show legend | checkbox | on | — |

The **Time** tab sets a fixed window, the aggregation, the interval and viewer filters; the **Series** tab sets per
datastream the label, colour, unit, decimals, style, axis (left or right), points, fill and visibility, plus the
legend position and legend values — see [time window](widgets.md).

- **Automatic axis**: the range of the shown values plus 8 % on each side; with bars the axis starts at 0.
- **Right axis**: series set to the right axis get their own scale on the right, with their units at the top; the
  legend marks them with ▸.
- **Limits** are drawn as dashed lines: amber for the warning limit, red for the action limit.
- **Min–max band**: with automatic or average aggregation, a light band shows the minimum and maximum of each bucket.
- **Doubtful readings** (quality other than *ok* and *backfilled*) are marked with orange dots; values replayed
  from a device's offline buffer count as good data, as in the period tables.
- **Legend**: a click on a series hides or shows it until the widget reloads (at the next refresh or new value); to hide a
  series permanently use *Hidden* on the Series tab or the viewer filter *series*. With *Legend values* each entry shows
  min, max, avg, total or latest of the points shown.
- **Pointer**: moving over the chart shows a cursor line and, under the chart, the time and the value of every series
  at that time (with the bucket's minimum and maximum).
- The line under the legend names the window and the aggregation, for example *7 d · average/15 min*.
- **Without data**: *no data in this range*.

## Bars over time

Aggregated bars per interval for each datastream, side by side or stacked — for example pieces per hour.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Bars over time | — |
| Range (empty = dashboard range) | list | (default) | 1h, 6h, 8h, 12h, today, 24h, 7d, 30d, 90d |
| Decimals | number | 2 | any |
| Stacked | checkbox | off | — |

- **Aggregation** (Time tab): avg, min, max, sum or count per bar; *auto* and *none* draw the average.
- **Aggregation interval** (Time tab): 1m, 5m, 15m, 1h, 6h or 1d; *auto* divides the window into about 40 bars of
  whole minutes. Over long windows the interval is raised so that there are at most 2000 bars.
- **Stacked** adds the series on top of each other; negative values count as 0 in a stack. Side by side, bars start
  at 0 and may go below it.
- A coloured key of the series is shown above the bars, with the window and the aggregation, for example
  *today · sum/1 h*. Each bar has a tooltip with the series and the value.
- **Without data**: *no data in this range*.

## State timeline

Coloured bands of states over time, one row per datastream — for example stopped, running and fault of machines.

| Setting | Type | Default | Allowed values |
|---|---|---|---|
| Title | text | State timeline | — |
| Range (empty = dashboard range) | list | (default) | 1h, 6h, 8h, 12h, today, 24h, 7d, 30d, 90d |
| States (value=Label:#colour, comma separated) | list | `0=Stopped:#94a3b8, 1=Running:#16a34a, 2=Fault:#dc2626` | up to 40 entries; colours as hex |

- The timeline uses the raw readings: each reading's state lasts until the next reading, the last one until the end
  of the window. The aggregation settings of the Time tab do not apply.
- A value without an entry is grey; its tooltip shows the value and the time.
- The row label is the series label or the datastream key. A key of all states and the window is shown above.
- **Without data**: an empty row.

## Heatmap (hour × day)

Hourly averages of the first bound datastream laid out as a grid: one row per day, one column per hour — daily and
weekly patterns at a glance.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Heatmap (hour × day) | — |
| Range | list | 7d | 7d, 30d |
| Decimals | number | 2 | any |
| Axis minimum (empty = auto) | number | — | the value drawn blue |
| Axis maximum (empty = auto) | number | — | the value drawn red |

- The colour goes from blue (low) to red (high) between the axis minimum and maximum, or between the lowest and
  highest hourly average when they are empty.
- Hours without readings are grey. Each cell has a tooltip with the day, the hour and the value.
- The heatmap uses its own range; the dashboard range does not apply.
- **Without data**: *no data*.

## Compare values

A horizontal bar with the latest value of each bound datastream.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Compare values | — |
| Decimals | number | 2 | any |
| Axis minimum (empty = auto) | number | — | any |
| Axis maximum (empty = auto) | number | — | any |
| Warning limit (high) | number | — | any |
| Action limit (high) | number | — | any |

The automatic scale runs from 0 (or the lowest value, if negative) to the highest value plus 5 %. A bar and its value
take the threshold colour in a zone, otherwise the series colour. **Without data**: an empty bar and `—`.

## Share (donut)

The latest values as shares of their sum, as a donut with a list of the values and percentages.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Share (donut) | — |
| Decimals | number | 2 | any |

Negative and missing values count as 0. **Without data**: an empty ring.

## Pie / doughnut

The latest values as shares of their sum, as a pie or a doughnut with a legend.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Pie / doughnut | — |
| Decimals | number | 2 | any |
| Doughnut | checkbox | on | off draws a full pie |
| Show percent | checkbox | on | percentages inside segments larger than 6 % |

**Legend position** (Series tab): right, bottom or none; the legend lists value and percentage. Each segment has a
tooltip. Negative and missing values count as 0. **Without data**: nothing is drawn.

## Polar area

The latest values as sectors of equal angle whose radius follows the value — the largest absolute value reaches the
edge.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Polar area | — |
| Decimals | number | 2 | any |

**Legend position** (Series tab): right, bottom or none. **Without data**: only the grid circles.

## Radar

The latest values on radial axes, one axis per datastream — for example the qualities of a product or the loads of
phases.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Radar | — |
| Decimals | number | 2 | any |
| Axis maximum (empty = auto) | number | — | the value at the edge; auto = the largest value plus 10 % |

The radar needs **at least 3** bound datastreams; with fewer it shows *bind at least 3 datastreams*. Each axis is
labelled with the datastream key and its value. Values below 0 are drawn at the centre.
