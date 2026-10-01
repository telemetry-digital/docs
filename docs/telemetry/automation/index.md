---
title: Automation and alarms
slug: automation
sidebar_position: 6
tags: [automation, alarms, flows]
---

telemetry.digital watches your data and acts on it: it raises alarms and notifies people, runs automation flows,
controls smart-home devices and keeps an incident log of what went wrong and why.

## Pages in this section

1. [Flows](flows.md) — visual automation in the style of Node-RED, built into the server.
2. [Alarms, notifications and incidents](alarms.md) — alarm rules, escalation, notification channels, incidents
   with cause and corrective action.
3. [Home page and scenes](home-and-scenes.md) — control discovered devices, scenes, smart-home flows.

## Rules of the house

- **Every action is audited** with its origin — the flow, its version and the node, or the rule.
- **Commands keep their rules** when automation sends them: the permission, the four-eyes policy and the device's
  acknowledgement apply as when a person sends them.
- **Deploying a flow needs a reason**, and flows that send commands need the command permission too.

!!! danger "Automation is not a safety system"
    A flow or an alarm can be late or fail — a network outage, a full queue, an undelivered e-mail. Never use them as
    a safety function. Undelivered alarms open an incident so that the failure itself is not silent.
