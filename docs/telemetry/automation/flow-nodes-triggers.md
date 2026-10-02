---
title: "Flow nodes: triggers"
slug: flow-nodes-triggers
sidebar_position: 3
tags: [automation, flows, nodes, triggers]
---

Trigger nodes start messages. They have no input and one output. A flow needs at least one enabled trigger to be
deployed; a [subflow](flow-subflows.md) has none — it starts at its *Subflow input*.

Every trigger has a ▶ button for [test messages](flow-editor.md). Disabled triggers never fire. In the tables,
*Default* is what a new node starts with and what the server fills in when you leave the field empty.

## Datastream value

Starts a message for every value stored in a datastream.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Datastream | datastream | — | a datastream of your organization | required |
| Only when the value changes | yes/no | no | — | drops a value equal to the last one this node passed |
| At most one message every (s) | number | — | 0–86400 | values arriving sooner are dropped, not delayed; empty or 0 = no limit |

| Message field | Content |
|---|---|
| `value` | the stored value (null when the observation has no value) |
| `topic` | `Asset / key`, for example `Mixer / temp` |
| `ts` | the measurement time |
| `quality` | the quality flag of the value |
| `source` | the datastream's name |

## Alarm

Starts a message when an alarm is raised or cleared.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Events | several choices | raised | raised, cleared, acknowledged | see the note below |
| Severity | choice | (any) | (any), warning, action, critical | only alarms of this severity |
| Only this datastream | datastream | — | a datastream | empty = every datastream |

| Message field | Content |
|---|---|
| `value` | the alarm value |
| `topic` | the datastream's name |
| `msg.event` | `raised` or `cleared` |
| `msg.severity` | `warning`, `action` or `critical` |
| `msg.alarm_id` | the alarm's identifier |

When an alarm rule's value crosses the action limit while a warning alarm is active, the alarm's severity rises to
*action* and the trigger receives a second *raised* event with the severity `action`.

!!! note "Acknowledgements"
    The option *acknowledged* can be selected, but acknowledging an alarm does not currently start a message.

## Device connection

Starts a message when a device connects or disconnects.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Device (empty = any) | device | — | a device | — |
| Events | several choices | connected, disconnected | connected, disconnected | — |

| Message field | Content |
|---|---|
| `value` | `true` when connected, `false` when disconnected |
| `topic` | the device's name (or its external identifier) |
| `msg.event` | `connected` or `disconnected` |

## Entity

Starts a message when a smart-home entity (found by [automatic device discovery](../devices/automatic-discovery.md))
changes its state.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Entity | entity | — | an entity | required |
| Only from state (empty = any) | text | — | up to 500 characters | for example `off` |
| Only to state (empty = any) | text | — | up to 500 characters | for example `on` |
| Also when only attributes change | yes/no | no | — | fires on a report with the same state, for example a new battery level |

| Message field | Content |
|---|---|
| `value` | the new state; a number when the state is numeric, otherwise text such as `on`, `open`, `locked` |
| `topic` | the entity's name |
| `msg.from` | the previous state |
| `msg.entity_id` | the entity's identifier |
| `msg.available` | `true` while the entity's device is available |
| `msg.<attribute>` | the entity's simple attributes (numbers, texts, true/false), for example `msg.battery`; names are reduced to letters, digits and `_` |

*From* and *to* filters match only real changes. The previous state is remembered in memory: after a server restart
the first change of an entity has an empty `msg.from` and does not match a *from* filter.

## Camera event

Starts a message when a camera reports an event over ONVIF — motion, tampering, line crossing, intrusion, a digital
input — or when a camera is marked (by a person or by a [Camera mark](flow-nodes-actions.md) node).

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Camera (empty = any) | camera | — | a camera | — |
| Kinds | several choices | motion, tamper, line, intrusion, input | motion, tamper, line, intrusion, input, mark, other | — |
| When | several choices | start | start, end | a single-moment event counts as *start* |

| Message field | Content |
|---|---|
| `value` | `true` at the start, `false` at the end |
| `topic` | the camera's name |
| `msg.kind` | the kind of event |
| `msg.phase` | `start`, `end` or `pulse` |
| `msg.camera_id` | the camera's identifier |
| `msg.event_id` | the event's identifier |
| `msg.label` | the event's label, for example the text of a mark |

See [Events and motion](../video-nvr/events-and-motion.md) for how cameras report events.

## Command result

Starts a message when a device command ends.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Device (empty = any) | device | — | a device | — |
| Statuses | several choices | failed, expired | acked, failed, expired, rejected | — |

| Message field | Content |
|---|---|
| `value` | the final status |
| `topic` | the device's name |
| `msg.command_id` | the command's identifier |
| `msg.method` | the command's method, for example `write` or `turn_on` |
| `msg.error` | the error text, empty when none |
| `msg.device_id` | the device's identifier |

Use it to react when a command does not get through, for example to notify the shift.

## MQTT in

Starts a message for every MQTT message on a topic filter of an MQTT account or bridge — for devices without
automatic device discovery.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| MQTT account or bridge | MQTT source | — | an MQTT account or bridge of your organization | required |
| Topic filter | text | `#` | an MQTT filter; `+` and `#` allowed | required; relative to the account or bridge |

| Message field | Content |
|---|---|
| `topic` | the MQTT topic of the message |
| `value` | a JSON number, text, true/false or null in the payload; otherwise the payload as text |
| `msg.<field>` | the members of a JSON object payload; nested objects are joined with `_` — `{"ENERGY":{"Power":5}}` gives `msg.ENERGY_Power` |

Payloads are read up to 64 KiB. At most 40 fields and three levels of nesting are taken. For an MQTT account,
retained messages already on the broker are delivered when the flow starts. When the subscription fails, the debug
panel shows *MQTT in not subscribed* with the reason.

## Schedule

Starts a message at an interval or at times of day, in the organization's time zone.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| When | choice | interval | interval, daily | — |
| Every (s) | number | 60 | 1–604800 | *interval* only; counted from when the flow starts |
| Times of day (HH:MM, comma separated) | times | `08:00` | for example `06:00, 14:00, 22:00` | *daily* only; required |
| Weekdays | several choices | all seven | mon … sun | for *interval*, ticks on other days are skipped |

| Message field | Content |
|---|---|
| `value` | the time of the tick in seconds since 1970 |
| `topic` | the node's name, or its identifier |
| `source` | the node's name |

## Incoming webhook

Starts a message when another system sends an HTTP `POST` to the node's address.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| HMAC secret (empty = token in the URL only) | text | — | up to 500 characters | when set, every call must be signed |

After you save, the property panel shows the **URL**:
`https://<your server>/api/v1/flow-hooks/fh_…`. The address is derived from the server's secret key, the flow and
the node identifier: it stays the same across versions, and it answers only while the deployed version contains the
node. Treat it like a password.

| Request | Rule |
|---|---|
| Body | a JSON object, up to 64 KiB; an empty body is allowed |
| Signature | with a secret: header `X-Signature: sha256=<hex>` — the HMAC-SHA256 of the raw body with the secret |
| `value`, `topic` | become the message's value and topic |
| Other members | become `msg.<name>` (at most 50; names reduced to letters, digits and `_`); objects and lists arrive as JSON text |

| Answer | Meaning |
|---|---|
| 202 | accepted |
| 400 | the body is not a JSON object |
| 403 | the signature does not match, or the flow's queue is full (*flow is busy*) |
| 404 | no deployed flow has this address |
| 413 | the body is larger than 64 KiB |

Refused signatures are written to the server's security log.

## Manual inject

Starts a message when you press its ▶ button in the editor, or through the API
(`POST /api/v1/flows/{slug}/inject`).

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Value (expression) | expression | `0` | an [expression](flow-expressions.md) | used when the test message has no value |
| Topic | text | — | up to 500 characters | see the note below |

!!! note "Topic of a manual inject"
    A test message always carries a topic — the one you type, or else the node's name — so the *Topic* setting of
    the node is not used at present. Set the topic in the test dialog, or with a *Change* node after the inject.
