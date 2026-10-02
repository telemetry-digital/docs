---
title: Flows
slug: flows
sidebar_position: 1
tags: [automation, flows]
---

Flows are visual automation in the style of Node-RED, built into the server: triggers, logic and actions wired on a
canvas, running on the server all the time. There is **no separate runtime and no user code** — every node comes from
a closed library, and calculations use a small expression language with limits.

## Quick start

1. **Flows → New flow**, name it, pick *Example: value above a limit for a while → notify* and give a reason.

    ![The New flow dialog with name, Start with and a reason](img/new-flow.webp)

2. Click the *Datastream value* node and choose the datastream; click *Notify* and choose a channel.

    ![The flow editor with the True for node selected and its settings on the right: name, condition value > 8, for 600 seconds and Disabled](img/flow-node-settings.webp)

    *A click on a node opens its settings on the right.*

3. Press ▶ on the trigger to send a test message. With **Dry run** checked, the debug panel shows what the actions
   *would* do, and nothing is executed.
4. **Deploy** with a reason. The flow runs until you **Stop** it; the header shows `running vN`.

Editing a running flow saves a new draft; the deployed version keeps running until you deploy again.

![The flow editor: the node palette with triggers and logic on the left; on the canvas a Datastream value node wired through True for and Rate limit to Notify, Debug and Incident; the flow details on the right; the Debug panel below; Dry run, Versions, Export, Save, Deploy and Stop above](img/flow-editor.webp)

*A running flow: above 8 °C for 10 minutes → notify and open an incident.*

![The dialog Save a new version: the running version keeps running until you deploy, and a field for the reason](img/flow-save-reason.webp)

## Nodes

| Group | Nodes |
|---|---|
| Triggers | Datastream value, Alarm, Device connection, Schedule, Incoming webhook, Manual inject, Camera event, Entity, Command result, MQTT in |
| Logic | Condition, Switch, Change, Math, Template, Delay, Debounce, Rate limit, Deadband, Aggregate, Join, Counter, Latch, Time window, True for, Split, Subflow |
| Actions | Store value, Device command, Device attribute, Notify, Incident, HTTP request, Debug, Mark on camera, Entity action, Scene, MQTT out |
| Other | Comment |

A few that do a lot:

- **True for** — one message when a condition has held for N seconds, one when it stops ("above 8 °C for 10 minutes").
- **Join** — keeps the latest value per topic so one expression can compare several datastreams.
- **Aggregate** — average, minimum, maximum, sum or count over a window.
- **HTTP request** — only to hosts on the allow-list (*Flows → Settings*).
- **Device command** — runs with the permissions of the flow's owner and under the four-eyes policy.

![A flow with two chains: Schedule at 06:00 → Template → Notify by webhook, and Datastream value → Deadband → Debug, with a debug entry below](img/flow-schedule.webp)

*A morning report by schedule, and a deadband on a meter value.*

## Expressions

```
value * 1.8 + 32
msg.temp > msg.limit + 2 && quality == 'ok'
if(value > 80, 'hot', 'ok')
fmtTime(ts, 'HH:mm')
```

Expressions see the message (`value`, `topic`, `ts`, `quality`, `msg.<field>`) and flow memory (`flow.<name>`) and
nothing else: no loops, no input or output, with limits on length and evaluation steps.

## Subflows and groups

A **subflow** is a reusable part — for example *notify the shift and open an incident* — built once and used in many
flows as one node. A flow pins the subflow's version when it is saved, so changing a subflow does not change running
flows until you deploy them again. **Groups** frame nodes in a named, coloured rectangle to keep the canvas tidy.

## Governance

- Every save is a **new version** with a reason; *Versions* lists them and *Restore* brings an old one back as a
  draft. Flows move between servers with Export and Import.

    ![The Versions dialog of a flow: version 2 deployed and version 1 draft, each with the time it was saved, the author and the reason](img/flow-versions.webp)

- **Deploy** needs a reason and the permission to edit content; flows with command or attribute nodes also need
  `device.command`. With the four-eyes policy such a deploy becomes a change request that another user approves.
- A flow with problems (an unknown node, a bad expression, a missing reference, a host not on the allow-list, a loop
  without a delay) can be saved as a draft but **cannot be deployed**.
- Every action is in the audit trail with the origin *flow, version, node, trigger*.

## Converting automation rules

Older **automation rules** ("condition → action") convert into an equivalent draft flow: *Flows → New flow → Start
with: Converted automation rule*. Whatever has no flow equivalent is listed before the flow is created. Stop the rule
once the flow runs, so that the actions do not happen twice.

## Limits

200 nodes and 600 wires per flow, 500 deployed flows per organization, a queue of 1 000 messages per flow (overflow
is counted, never silent). The debug panel keeps the last 500 entries per flow; counters, latches and flow memory
survive restarts.
