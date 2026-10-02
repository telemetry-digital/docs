---
title: "Flow nodes: logic"
slug: flow-nodes-logic
sidebar_position: 4
tags: [automation, flows, nodes, logic]
---

Logic nodes have one input. They filter, route, change, time, aggregate or remember messages. Expressions and
templates in their settings use the [flow expression language](flow-expressions.md).

When a node cannot process a message — an expression fails, a value is not a number — it writes an **error** to the
debug panel, counts it, and that message goes no further. The flow carries on with the next message.

## Condition

Sends the message to the first output when the condition is true, otherwise to the second.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Condition | expression | `value > 0` | an expression | required; null, 0, empty text and false are false |

Outputs: **true**, **false**.

## Switch

Routes the message by rules: one output per rule plus a last output, *else*, for messages no rule matched.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Rules (one condition per line) | rules | `value < 10`, `value >= 10` | 1–10 conditions | required; the number of outputs follows the rules |
| Check | choice | first | first, all | *first*: only the first matching rule; *all*: every matching rule gets a copy |

A rule whose expression fails writes an error and counts as not matched; the other rules are still checked. When
you remove rules, wires from outputs that no longer exist are removed.

Example — three temperature bands: `value < 5`, `value < 25`, and the *else* output for everything warmer.

## Change

Sets, deletes or renames fields of the message, in the order of the list.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Changes | list of changes | set `value` to `value` | 1–20 changes | required |

| Operation | Field | Third column | Notes |
|---|---|---|---|
| set | `value`, `topic` or `msg.<name>` | an expression | `topic` is always stored as text |
| delete | `msg.<name>` | — | `value` and `topic` cannot be deleted; set them to `null` |
| rename | `value`, `topic` or `msg.<name>` | the new field name | moves the value to the new field |

Field names after `msg.` start with a letter or `_` and have at most 40 letters, digits or `_`. When one change
fails, the message stops there.

Example — give a datastream message a short topic before a *Join*: set `topic` to `'t_in'`.

## Math

Computes an expression and stores the result.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Expression | expression | `value * 1` | an expression | required |
| Into | text | `value` | `value`, `topic` or `msg.<name>` | where the result goes |

Example — Celsius to Fahrenheit: `value * 1.8 + 32`.

## Template

Builds a text with `{{ expression }}` placeholders and stores it.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Text | template | `{{ topic }} = {{ value }}` | up to 4 000 characters | required |
| Into | text | `value` | `value`, `topic` or `msg.<name>` | where the text goes |

Whole numbers are written without decimals, other numbers with up to six significant digits; use `fixed(value, 1)`
for a fixed number of decimals. The result may be at most 4 096 bytes.

Example: `{{ topic }} is {{ fixed(value, 1) }} °C at {{ fmtTime(ts, 'HH:mm') }}`.

## Delay

Passes every message on after a fixed time.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Delay (s) | number | 5 | 0.01–86400 | required |

Each message waits on its own; nothing is dropped. Waiting messages are lost when the flow is redeployed or stopped
or the server restarts. A *Delay* makes a loop legal.

## Debounce

Passes only the last message after the input has been quiet for the given time.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Quiet time (s) | number | 2 | 0.01–86400 | required |

Every new message restarts the quiet time, across all topics. A *Debounce* makes a loop legal.

## Rate limit

Passes at most N messages per period; the rest are dropped.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Messages | number | 1 | 1–10000 | required |
| Per (s) | number | 60 | 0.1–86400 | required; a sliding window |
| Separately per topic | yes/no | no | — | a separate window for each topic |

Every dropped message writes a *drop* entry (*rate limit: message dropped*) to the debug panel. A *Rate limit* makes
a loop legal. Example: at most one e-mail per 15 minutes — 1 message per 900 s in front of a *Notify*.

## Deadband

Passes a value only when it differs from the last passed value of the same topic by at least the band.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Band | number | 0.5 | 0 or more | required |
| Band in % of the last value | yes/no | no | — | the band is a percentage of the last passed value |

The first value of each topic always passes. A value that is not a number is an error. Example: with a band of 0.5
the values 20.0, 20.3, 20.6 pass as 20.0 and 20.6.

## Aggregate

Collects the values of a time window and sends one message with the result at the end of the window, separately for
each topic.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Window (s) | number | 60 | 1–86400 | required |
| Function | choice | avg | avg, min, max, sum, count, first, last | avg = the mean of the values |

The window starts with the first value of a topic. The result message is the last message of the window with the
result as `value`, the current time as `ts` and the number of values as `msg.count`. A value that is not a number is
an error. Open windows are lost on redeploy, stop or restart. An *Aggregate* makes a loop legal.

## Join

Keeps the latest value of each topic; once all expected topics are present, every message carries them all.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Topics (comma separated) | text | — | up to 500 characters | required; for example `t_in, t_out` |
| Forget values older than (s, 0 = never) | number | 0 | 0 or more | a stale topic counts as missing |

Every incoming message updates the memory of its topic. While a listed topic is missing (or too old), nothing is
sent. Then each incoming message goes on with `msg.<topic>` for every listed topic — characters other than letters,
digits and `_` become `_`.

!!! tip "Short topics"
    A datastream trigger's topic is `Asset / key`, which becomes `msg.Asset___key`. Put a *Change* node after each
    trigger that sets the topic to a short name, for example `'t_in'`, and join on those names.

Example — compare two sensors: join `t_in, t_out`, then a *Condition* `msg.t_in - msg.t_out > 5`.

## Split

Sends one message per element of a list or JSON object, or per part of a text.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| List (expression) | expression | `value` | an expression | what to split |
| Separator for texts (empty = JSON) | text | — | up to 500 characters | for example `;` |
| At most | number | 100 | 1–1000 | further elements are skipped and an *info* entry says how many there were |

- With a separator, the text is cut at it and every part is trimmed.
- Without one, a text is read as JSON. A list gives one message per element; an object one message per member,
  sorted by name; anything else one message.
- Each message has the element as `value`, plus `msg.index` (from 0), `msg.count` and, for objects, `msg.key`.
  Nested lists and objects arrive as JSON text.

Example: a gateway sends `[21.5, 22.0, 19.8]` — *Split* sends three messages with index 0, 1 and 2.

## Counter

Counts messages, or adds an expression; the count becomes the value.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Add (expression) | expression | `1` | an expression | a result that is not a number adds 1 |
| Reset when (expression, empty = never) | expression | — | an expression | when true, the count becomes 0 and a message with value 0 is sent |

The count is kept across redeploys and restarts (saved every 30 seconds and when the flow stops), as long as the node
keeps its identifier. Example — energy pulses per day: add `1`, reset when `hour() == 0 && minute() == 0`.

## Latch

Remembers on or off: set when one condition is true, reset when the other is. Sends a message only when the state
changes.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Set when | expression | `value > 80` | an expression | required; checked while off |
| Reset when | expression | `value < 70` | an expression | required; checked while on |

The output message has `value` `true` or `false` and the original value in `msg.input`. The state is kept across
redeploys and restarts. With the defaults it is a thermostat-like hysteresis: on above 80, off below 70.

## Time window

Sends messages inside a time window to the first output and the others to the second, in the organization's time
zone.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| From (HH:MM) | time | `06:00` | 00:00–23:59 | required; inclusive |
| To (HH:MM) | time | `22:00` | 00:00–23:59 | required; exclusive |
| Weekdays | several choices | all seven | mon … sun | checked against the current day |

A window whose *To* is earlier than *From* runs over midnight, for example `22:00`–`06:00`. Outputs: **inside**,
**outside**.

## True for

Sends one message when a condition has been true for the whole time, and one when it stops being true.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Condition | expression | `value > 80` | an expression | required |
| For (s) | number | 120 | 0–86400 | required |

- When the condition becomes true, the time starts. If a message makes it false before the time is up, nothing is
  sent and the time starts again with the next true message.
- When the time is up, output **became true** gets the latest message.
- When the condition later turns false, output **became false** gets that message.

The condition is checked only when a message arrives: a sensor that stops sending keeps the last state. Pending
times are lost on redeploy, stop or restart. A *True for* makes a loop legal.

Example — *above 8 °C for 10 minutes*: condition `value > 8`, for 600 s.

## Subflow

Runs a [subflow](flow-subflows.md): messages enter at its input and leave at its outputs.

| Setting | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Subflow | subflow | — | a subflow of your organization | required |
| Version (empty = latest) | number | — | 0 or more | filled in with the current version number when you save |

The node has as many outputs as the subflow has numbered outputs (up to 4).
