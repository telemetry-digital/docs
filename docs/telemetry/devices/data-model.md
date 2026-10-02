---
title: Sites, assets and datastreams
slug: devices-data-model
sidebar_position: 1
tags: [devices, data, datastreams, assets, sites, alarms]
---

Measurements are not stored "per device" but per **datastream**. A datastream belongs to an **asset** (a fridge, a
room, a meter, a machine), an asset belongs to a **site** (a building, a plant, a branch). A device only *feeds*
datastreams: when you replace a broken sensor, you assign the same datastreams to the new device and the history
continues without a break.

```text
site ─┬─ asset ─┬─ datastream (key temp)  ◄── fed by device A since 2026-03-01
      │         └─ datastream (key rh)    ◄── fed by device A since 2026-03-01
      └─ asset ─── datastream (key energy) ◄── fed by connector "modbus-meter"
```

All three live under **Assets** in the main menu, with the sub-pages **Sites**, **Assets**, **Alarm rules** and
**Automation** (automation rules are described in [Alarms, notifications and incidents](../automation/alarms.md)).

## Who can do what

| Action | Permission |
|---|:---:|
| See sites, assets, datastreams, alarm rules and values | `data.read` |
| Create and edit sites, assets, datastreams and alarm rules | `config.write` |
| Assign a datastream to a device, remove an assignment | `device.manage` |

Every change asks for a **reason** (at most 200 characters) and is written to the audit trail with the old and new
values. With the four-eyes policy (see [Approvals](#four-eyes-approval)) alarm limits and assignments wait for a second
person.

## Sites

**Assets → Sites** lists every site with its name, time zone, address, the number of assets (a link to them) and the
creation time. **New site** and **Edit** open the same dialog.

| Field | Default | Limits | Meaning |
|---|:---:|:---:|---|
| Name | — | required, 1–128 characters, unique in the organization | the name shown everywhere |
| Time zone | `Europe/Bratislava` in the form, `UTC` when sent empty | IANA name, letters, digits and `_ + - /`, at most 64 characters | local time of the site, used for day boundaries in reports and period tables |
| Address | — | at most 200 characters | free text, shown next to the site |
| Reason (audit trail) | — | required, at most 200 characters | why you made the change |

A site cannot be deleted from the interface.

## Assets

**Assets** shows the assets of one site; choose the site in the **Site** selector at the top (its time zone and
address are shown next to it). Each asset is a card with its name, its type and a table of its datastreams (key,
quantity with unit, kind, expected interval). **+ datastream** on a card adds a datastream; clicking a datastream
opens its detail.

![The New asset dialog with type, name and a reason](img/new-asset.webp)

| Field | Default | Limits | Meaning |
|---|:---:|:---:|---|
| Type | — | required, at most 32 characters, stored in lower case | what the asset is; suggestions: fridge, freezer, room, meter, machine, building, vehicle — any word is allowed |
| Name | — | required, 1–128 characters, unique on the site | the name shown in dashboards, reports and alarms |
| Reason (audit trail) | — | required | why you made the change |

!!! note "Create a site first"
    The **New asset** button appears only when a site exists and your role has `config.write`.

## Datastreams

A datastream is one measured or reported value of an asset — the temperature of a fridge, the active energy of a
meter, the state of a door.

### New datastream

| Field | Default | Limits | Meaning |
|---|:---:|:---:|---|
| Key (what the device publishes) | — | required, `^[a-z][a-z0-9_]{0,31}$`: a lower-case letter, then letters, digits or `_`, at most 32 characters; unique on the asset | the key in the device's telemetry, for example `temp` |
| Kind | gauge | gauge, counter, state, event | how the value behaves (see below) |
| Quantity | — | required, at most 64 characters, stored in lower case | what is measured; suggestions: temperature, humidity, energy_active_import, power_active, pressure, state, co2 |
| Unit | — | at most 16 characters | shown after the value, for example `°C`, `kWh`, `%` |
| Expected interval (gap detection) | — | at most 32 characters, a duration such as `5 minutes`, `30 seconds`, `1 hour` | how often a value should arrive; empty means no gap detection |
| Required accuracy | — | any number | the accuracy the measurement must have, recorded with the datastream |
| Reason (audit trail) | — | required | why you made the change |

| Kind | Use it for |
|---|---|
| gauge | an instantaneous value: temperature, pressure, power |
| counter | a value that only grows: an energy or water meter reading |
| state | a discrete state: on/off, open/closed, a mode |
| event | something that happened at a moment: a button press, a trip |

!!! tip "The expected interval makes missing data visible"
    With an expected interval, data that does not arrive becomes a visible **gap** in charts and reports instead of a
    silent hole. A gap opens when no value has arrived for 1.5 × the expected interval, and closes when data resumes
    or is backfilled. Set it to the device's reporting period.

### Datastream detail

Clicking a datastream opens its detail:

- **Site**, **Quantity** with unit, **Expected interval** (or "no gap detection"), **Accuracy**.
- **Source device** — the device that currently feeds the datastream and since when, with a link to the device, or
  "none — assign one on the device page".
- **Last value** — value, unit, measurement time and its **quality**.
- **Rules** — automation rules that read the datastream or derive (write) it, with their state.
- **Id** — the datastream's identifier, for the API.
- **Edit** (with `config.write`): change **Unit** and **Expected interval**, with a reason. The key, the kind and the
  asset cannot be changed after creation.

#### Quality of a value

| Quality | Meaning |
|---|---|
| ok | a normal measurement |
| backfilled | a value replayed from the device's offline buffer (`bf: 1`) |
| clock_suspect | the device's time stamp was more than 5 minutes in the future |
| sensor_fault | the sensor reported a fault |
| out_of_range | the value was outside the valid range |
| stale | the value is old |
| simulated | a simulated value |

## Alarm rules (limits)

Limits are **alarm rules** on a datastream. They are versioned: saving a rule of the same type on the same datastream
closes the current version and creates the next one, so you always know which limits applied when.

You create them in two places:

- **Assets → Alarm rules → New rule** — choose any datastream; this dialog also offers an **escalation policy**.
- The datastream detail → **New rule version** — for the open datastream.

| Field | Default | Limits | Meaning |
|---|:---:|:---:|---|
| Datastream | — | required | the datastream the rule watches (only in **New rule**) |
| Type | high | see the table below | what the rule detects |
| Warning limit | — | a number | first, lower severity limit |
| Action limit | — | a number | second, higher severity limit; types high and low need at least one of the two limits |
| Delay (s) | 0 | 0–604 800 (7 days) | how long the condition must last before the alarm is raised |
| Hysteresis | 0 | ≥ 0 | how far back the value must return before the alarm clears, so a value hovering at the limit does not flap |
| Escalation policy | none (global e-mail) | an active policy of the organization | who is notified and when (see [Alarms, notifications and incidents](../automation/alarms.md)) |
| Reason (audit trail) | — | required | why you made the change |

| Type | Detects |
|---|---|
| high | the value is at or above the warning limit (severity *warning*) or the action limit (severity *action*) |
| low | the value is at or below the warning limit (severity *warning*) or the action limit (severity *action*) |
| comm_loss | data stopped arriving |
| sensor_fault | the sensor reports a fault |
| low_battery | the battery is low |
| power_loss | the power supply failed |
| door_open | a door is open |

An alarm of type high clears when the value falls below the limit minus the hysteresis; an alarm of type low clears
when it rises above the limit plus the hysteresis.

!!! note "Which types are evaluated against values"
    The server compares every incoming value with the limits of rules of the types **high** and **low**. The other
    types can be recorded on a datastream, but in this version the server does not raise alarms from them on its
    own; for "no data", "door open too long" and similar conditions use an automation rule (templates *No data for
    10 minutes → notify* and *Door open for 5 minutes → alarm*).

**Assets → Alarm rules** lists the current versions: asset, datastream, type, warning, action, delay, hysteresis,
version (the reason as a tooltip) and since when. Clicking a row opens the datastream. In the datastream detail,
**Disable** closes the current version of a rule (with a reason); the rule stops applying, its history stays.

## Assigning datastreams to devices

On the device page, tab **Datastreams**, you assign the datastreams a device feeds (see
[The device page](device-page.md)):

- Each key the device publishes must match the key of an assigned datastream; other keys are kept as raw messages
  with the status *unknown key*.
- A datastream has one source at a time. Assigning it to another device **closes the previous assignment**; the
  message says "assigned (previous source closed)".
- **Remove** ends an assignment (with a reason). The history of values stays with the datastream.

## Four-eyes approval

Under **Settings → Security policy** an administrator (`user.admin`) can switch on four-eyes approval separately for
commands, limits and assignments. Then:

- a new alarm rule version or disabling a rule (limits),
- assigning or removing a datastream (assignments),

is recorded as a **change request** — "waiting for approval by a second person" — and takes effect only after a
different user approves it under **Settings → Approvals**.
