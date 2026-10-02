---
title: Value widgets
slug: dashboard-widgets-values
sidebar_position: 3
tags: [dashboards, widgets, gauges, reference]
---

Value widgets show the **latest value** of datastreams — or, for the aggregated value, one number over a time range.
The tables list the settings on the **Data** tab. Every widget on this page also has the shared Appearance and Actions settings,
the Series tab when it binds datastreams, and except for the LED indicators and the state indicator also *Value colour*, *Show datastream
name* and *Conditional formatting* — see the [widget catalogue](widgets.md). None of these widgets sends commands or
offers an export.

## Latest value

The latest value of each bound datastream with its unit, the datastream name, the time of the reading and a 6-hour
sparkline. With several datastreams the values are listed below each other.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Latest value | — |
| Decimals | number | 2 | any; a series can set 0–6 |
| Warning limit (high) | number | — | any |
| Action limit (high) | number | — | any |
| Warning limit (low) | number | — | any |
| Action limit (low) | number | — | any |
| Sparkline (6 h) | checkbox | on | — |

- **Colour** of a value: a matching conditional-formatting rule, otherwise the threshold colour, otherwise *Value
  colour*, *Text colour* or the series colour.
- The sparkline is drawn only when the widget is at least 3 columns wide.
- A reading with a quality other than *ok* shows the quality after the time.
- **Without data**: `—` and *no value yet*.

## Value card

One large value of the first bound datastream with an optional icon, a trend arrow and a sparkline.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Value card | — |
| Icon | icon | none | an icon of the built-in catalogue |
| Decimals | number | 2 | any |
| Unit (empty = datastream) | text | — | replaces the datastream's unit |
| Trend arrow (vs 1 h ago) | checkbox | on | — |
| Sparkline (6 h) | checkbox | on | — |
| Sparkline range | list | 6h | 1h, 6h, 24h, 7d |

- The **trend arrow** compares the current value with the point of the sparkline range nearest to one hour ago: a
  green arrow up or a red arrow down with the difference, `→` when there is no change.
- The sparkline is drawn only when the widget is at least 3 columns wide.
- Conditional formatting can colour the value, the icon or the card background.
- **Without data**: `—` and *no value yet*.

## Aggregated value

One number computed from the first bound datastream over a time range — for example the pieces made today, the
average temperature of the shift, or the maximum pressure of the week.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Aggregated value | — |
| Icon | icon | none | an icon of the built-in catalogue |
| Decimals | number | 2 | any |
| Unit (empty = datastream) | text | — | replaces the datastream's unit |
| Function | list | avg | avg, min, max, sum, count, delta |
| Range (empty = dashboard range) | list | (default) | 1h, 6h, 8h, 12h, today, 24h, 7d, 30d, 90d |

The **Time** tab adds *Time window* (relative or fixed), *From* and *To* — see [time window](widgets.md).

| Function | Result |
|---|---|
| avg | the average of all readings in the range |
| min, max | the lowest and the highest reading |
| sum | the sum of all readings |
| count | the number of readings, shown without decimals and unit |
| delta | the change: the average of the last bucket minus the average of the first one (buckets of at least one minute, at most 2000 in the range) |

The widget names the function and the range under the value, for example *average · 24 h*. For the consumption of a
counter per day or month, a [period table](period-tables.md) gives exact values per period.

**Without data**: `—`.

## Gauge

A half-circle gauge per bound datastream between a minimum and a maximum, with warning and action zones.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Gauge | — |
| Decimals | number | 2 | any |
| Minimum | number | 0 | any |
| Maximum | number | 100 | any |
| Warning limit (high) | number | — | any |
| Action limit (high) | number | — | any |

The arc from the warning limit to the maximum is drawn amber, from the action limit to the maximum red; the value
takes the threshold colour. Values outside the range fill the arc to its end or not at all. **Without data**: an
empty arc and `—`.

## Radial gauge

A dial with ticks, coloured bands and a needle for the first bound datastream.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Radial gauge | — |
| Decimals | number | 2 | any |
| Minimum | number | 0 | any |
| Maximum | number | 100 | any |
| Major ticks | number | 10 | 2–20; four minor ticks between two major ones |
| Arc | list | 270 | 180, 240, 270 (degrees) |
| Warning limit (high) | number | — | any |
| Action limit (high) | number | — | any |

The band from the warning to the action limit (or to the maximum) is amber, from the action limit to the maximum red;
*Warning colour* and *Action colour* replace them. **Without data**: the needle rests grey at the start and the value
is `—`.

## Linear gauge

A scale with a pointer for the first bound datastream, horizontal or vertical, with warning and action zones.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Linear gauge | — |
| Decimals | number | 2 | any |
| Minimum | number | 0 | any |
| Maximum | number | 100 | any |
| Orientation | list | horizontal | horizontal, vertical |
| Warning limit (high) | number | — | any |
| Action limit (high) | number | — | any |

**Without data**: no pointer and `—`.

## Progress bar

A bar per bound datastream from the minimum to the maximum — for example the plan fulfilment with the plan as the
maximum.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Progress bar | — |
| Decimals | number | 2 | any |
| Minimum | number | 0 | any |
| Maximum | number | 100 | any |
| Orientation | list | horizontal | horizontal, vertical |
| Bar colour (empty = series colour) | colour | — | hex colour |
| Show value | checkbox | on | — |

A horizontal bar shows the minimum, the percentage and the maximum under it; a vertical bar shows the datastream key
under it. **Without data**: an empty bar and `—`.

## Liquid level

A vessel filled to the level of the first bound datastream, with the percentage, the value and optionally the
volume.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Liquid level | — |
| Decimals | number | 2 | any |
| Value at empty | number | 0 | any |
| Value at full | number | 100 | any |
| Shape | list | tank_vertical | tank_vertical, tank_horizontal, silo, hopper, basin, tank_spherical |
| Liquid colour | colour | `#3b82f6` | hex colour |
| Capacity (for volume, empty = none) | number | — | any |
| Volume unit | text | l | — |

With a capacity the widget shows *volume / capacity unit*, where the volume is the fill fraction times the capacity,
without decimals. A conditional-formatting rule with the target *Value colour* recolours the liquid. **Without
data**: an empty vessel and `—`.

## Thermometer

A thermometer with a scale for the first bound datastream.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Thermometer | — |
| Decimals | number | 2 | any |
| Scale minimum | number | −20 | any |
| Scale maximum | number | 50 | any |
| Warning limit (high) | number | — | any |
| Action limit (high) | number | — | any |
| Warning limit (low) | number | — | any |
| Action limit (low) | number | — | any |

Each limit is marked on the scale with a dashed line in its colour; the column is red and takes the threshold colour
in a zone. **Without data**: an empty column and `—`.

## Battery level

A battery symbol with the charge of the first bound datastream in percent.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Battery level | — |
| Decimals | number | 2 | any (the percentage is shown without decimals) |
| Minimum | number | 0 | the value at 0 % |
| Maximum | number | 100 | the value at 100 % |
| Low below | number | 30 | percent |
| Critical below | number | 15 | percent |

The charge is green, amber below *Low below* and red below *Critical below*. **Without data**: an empty battery and
`—`.

## Signal strength

Five signal bars from the first bound datastream.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Signal strength | — |
| Scale | list | rssi | rssi (dBm), percent |
| Show value | checkbox | on | — |

| Scale | Bars |
|---|---|
| rssi | 5 from −55 dBm, 4 from −65, 3 from −75, 2 from −85, 1 from −95, 0 below |
| percent | one bar per started 20 % |

Three or more bars are green, two amber, fewer red. The value is shown without decimals with the datastream's unit,
or dBm or % when the datastream has none. **Without data**: grey bars and `—`.

## Compass / wind

A compass rose with a needle. The first bound datastream is the direction in degrees (0 = north, clockwise); an
optional second datastream is the speed, shown under the direction with its unit.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Compass / wind | — |
| Decimals | number | 2 | any; for the speed (the direction has none) |

**Without data**: no needle and `—`.

## State indicator

Maps the latest value of each bound datastream to a labelled colour — for example *Stopped*, *Running*, *Fault*.

| Setting | Type | Default | Allowed values |
|---|---|---|---|
| Title | text | State indicator | — |
| States (value=Label:#colour, comma separated) | list | `0=Off:#6b7280, 1=On:#059669` | up to 40 entries |

- An entry has the form `value=Label:#colour`, for example `2=Fault:#dc2626`. Without a colour the state is grey.
- A number matches numerically (`1` matches 1.0); a true or false value matches `true` or `false`.
- A value without an entry is shown as the value itself, in grey; a datastream without a reading shows *no value*.
- Each datastream is a tile with a coloured dot, the label, the datastream name and the time of the reading.

## LED indicators

One LED per bound datastream, lit when the value equals the *on* value — for example the inputs of a controller.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | LED indicators | — |
| Value meaning on | number | 1 | any; true counts as 1, false as 0 |
| On colour | colour | `#16a34a` | hex colour |
| Off colour | colour | `#cbd5e1` | hex colour |
| Columns | number | 4 | 1–12 |

The datastream key is shown under each LED; the tooltip shows the name and the value. **Without data**: the LED is
off.

## Alarm count

The number of active alarms of the organization.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Alarm count | — |
| Severity | list | (default) = all | (default), action, warning |
| Icon | icon | bell | an icon of the built-in catalogue |

The count is green at 0, red when an action alarm is active, otherwise amber; conditional formatting can override it.
It updates when an alarm is raised or cleared. On a public link it counts only the alarms of the datastreams bound
by the dashboard's widgets, like the *Alarms* list.
