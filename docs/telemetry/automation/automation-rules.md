---
title: Automation rules
slug: automation-rules
sidebar_position: 11
tags: [automation, rules, conditions, actions]
---

Automation rules are simple **condition → action** rules over one or more datastreams: *when all (or any) of the
conditions hold — optionally for a hold time — fire the actions; when the condition ends, run the exit actions*. They
are under *Assets → Automation*, with a run log of what each rule did. For anything larger use [flows](flows.md); a
rule converts into an equivalent flow.

![Assets → Automation: rules with their state, condition, actions and last run, and the run log below](img/automation-rules.webp)

## Permissions

| To | Permission |
|---|---|
| See, create, edit, enable, disable, revoke and test rules | `config.write` |
| Create or edit a rule whose actions send device commands | `config.write` and `device.command` |
| Approve or reject a rule waiting for four eyes | `device.command`, and you are not the rule's last editor |

Every change asks for a reason and is audited (`rule.create`, `rule.update`, `rule.revoke`, `rule.approve`,
`rule.reject`).

## The rule list

| Column | Content |
|---|---|
| Rule | name, version and description |
| State | see below |
| Condition | the conditions joined with *and* / *or*, and the hold time |
| Actions | the enter actions, and the exit actions below them |
| Last run | the time and the event (*enter* or *exit*) of the last transition |
| Buttons | Edit, Approve and Reject (pending rules), Disable or Enable, Revoke |

| State | Meaning |
|---|---|
| watching | enabled and active; the condition is false |
| holding | the condition is true and the hold time or the cooldown is running |
| condition true | the rule fired *enter* and the condition still holds |
| pending approval | the rule sends commands and waits for a second person |
| rejected | the second person rejected it; edit it before you can enable it |
| disabled | switched off with a reason |
| revoked | ended for good |

## New automation rule

![The New automation rule dialog: template, name, description, inputs, condition with hold time and cooldown, actions on enter, actions on exit and a reason](img/new-automation-rule.webp)

| Field | Default | Allowed | Notes |
|---|:---:|:---:|---|
| Start from a template | (empty rule) | see *Templates* | only when creating |
| Name | — | 1–64 characters, unique | required |
| Description | — | up to 200 characters in the dialog | shown under the name |
| Inputs | — | up to 16 | name and datastream, see below |
| Fire when | all conditions hold | all, any | how the conditions combine |
| Hold time (s) | 0 | 0–604800 | the condition must hold this long before *enter* fires |
| Cooldown (s) | 0 | 0–604800 | minimum time between two *enter* firings |
| Conditions | — | up to 16 | none = always true |
| Actions on enter | — | up to 16 | run when the condition starts to hold |
| Actions on exit | — | up to 16 | at least one action in the two lists together |
| Reason (audit trail) | — | up to 200 characters | required |

**Test now** evaluates the rule as it is in the dialog against the current values, without running any action: it
shows whether the condition is true now, each input's value and age, inputs without data and expression errors.
**Create rule** (or **Save as new version** when editing) saves it.

### Inputs

| Field | Allowed | Notes |
|---|---|---|
| Name | starts with a lower-case letter, then lower-case letters, digits or `_`; up to 32 characters | unique in the rule; not a function name such as `min` |
| Datastream | a datastream of your organization | required |

Conditions and expressions use these names, for example `t_in - t_out`.

### Conditions

| Field | Allowed | Notes |
|---|---|---|
| Input | one of the rule's inputs | required |
| Operator | `>`, `>=`, `<`, `<=`, `==`, `!=`, `stale` | `stale` = no data for … seconds |
| Value | a number, or an expression over the inputs; for `stale` a number of seconds 1–31622400 | for example `30` or `t_out + 2` |

- A comparison uses the input's **latest stored value**. When the input has no value yet, or an expression in the
  value cannot be computed (an input without data, a division by zero), the condition is false.
- `stale` is true when the input has **no data for longer than the given seconds** — or no data at all.
- With *all*, every condition must be true; with *any*, one is enough. A rule without conditions is always true.

### Actions

| Action | Fields | Notes |
|---|---|---|
| Notify a channel | Channel; Subject (up to 200 characters); Text (up to 4 000 characters) | placeholders `{rule}`, `{event}`, `{inputs}` and `{<input name>}` |
| Call a webhook | URL (`http://` or `https://`, up to 1 024 characters); Secret (HMAC-SHA256, optional, up to 256 characters) | only hosts of the organization's allow-list (*Flows → Settings*) |
| Send a device command | Device; Key (writable register / node: lower-case letters, digits and `_`, starting with a letter, up to 32 characters); Value or expression (`20`, `true`, `t_out + 2`) | commanding: needs `device.command` |
| Raise an alarm | Severity (warning, action, critical; default warning); Escalation policy (default: none, global e-mail) | not allowed in the exit list |
| Write a derived datastream | Derived datastream; Expression (up to 512 characters) | the datastream must not be an input of the same rule |

Details of each action:

- **Notify a channel** sends through a channel of *Settings → Notifications*. Without a subject it sends
  `[ctrl32] rule {rule}: {event}`, without a text `Rule {rule} — {event}` and the inputs. `{event}` is `enter` or
  `exit`; `{inputs}` lists every input as `name = value` (or *(no data)*); `{t_in}` is the value of the input
  `t_in`. Every delivery is in the delivery log with the event `rule`.
- **Call a webhook** sends the same JSON message as a webhook channel (event `rule`, the rule's identifier, name,
  version, transition and input values), signed in `X-Ctrl32-Signature` when you give a secret. The host must be on the organization's allow-list
  (*Flows → Settings*), like the flows' *HTTP request* node — checked when the rule is saved and again when the
  webhook is sent; otherwise saving answers *webhook: host "…" is not on the organization's allow-list*. Add the
  hosts of existing rules to the list before you change them.
- **Send a device command** sends the method `write` with `{"key": <key>, "value": <value>}`. The command is valid
  for 5 minutes, recorded with the issuer `rule:<name>` and the reason *automation rule name*, and dispatched at once
  — the four-eyes check happened when the rule was approved. (Through the API you can also set another method or
  custom arguments.)
- **Raise an alarm** raises one alarm for the rule, with the first input's datastream and value. While it is not
  cleared, a new *enter* does not raise a second one. It is **cleared automatically** when the rule's condition ends,
  and when the rule is disabled, revoked, changed or waiting for approval. With an escalation policy the policy's
  steps run as for any alarm — see [Notifications and escalation](notifications.md); without one, the global
  e-mail recipients are told.
- **Write a derived datastream** stores the result of the expression. In the enter list it is written on *enter* and
  then **on every evaluation while the condition holds** — with no conditions, on every new input value. In the exit
  list it is written on *exit*.

## Templates

| Template | Inputs | Condition | Actions |
|---|---|---|---|
| High value → notify a channel | `value` | `value >= 30` | enter: notify; exit: notify |
| No data for 10 minutes → notify | `value` | `value stale 600` | enter: notify |
| Door open for 5 minutes → alarm | `door` | `door == 1`, hold 300 s | enter: warning alarm |
| Difference of two sensors → derived datastream | `t_in`, `t_out` | none | enter: derive `t_in - t_out` |
| Setpoint follows a measured value → device command | `t_out` | `t_out != -999`, cooldown 300 s | enter: command `setpoint` = `clamp(t_out + 2, 15, 25)` |

A template fills the dialog; you still choose the datastreams, channels and devices.

## Rule expressions

Values of conditions, command values and derived datastreams use a small **numeric** expression language:

| Element | Written as |
|---|---|
| Numbers and input names | `30`, `-2.5`, `t_in` |
| Operators | `+`, `-`, `*`, `/`, `%`, `^` and parentheses; unary minus |
| Functions | `abs(x)`, `min(a, b)`, `max(a, b)`, `round(x)`, `floor(x)`, `ceil(x)`, `sqrt(x)`, `clamp(x, lo, hi)` |

At most 512 characters. An input without a value, a division by zero, the square root of a negative number or a
result that is not a finite number is an error. There are no texts, comparisons or time functions here — use a
[flow](flow-expressions.md) for those.

## How rules run

- A rule is evaluated whenever one of its inputs gets a new value, and every 5 seconds while a hold time or cooldown
  is running or when it has a `stale` condition.
- **Enter** fires when the condition is true, has been true for the hold time, and the cooldown since the last
  *enter* has passed. **Exit** fires when the condition becomes false after *enter*.
- Alarms and derived values are handled at once. Notifications, webhooks and commands run in the background (at most
  16 rules at a time, 60 seconds per transition), so a slow e-mail server does not hold up incoming data.
- A failed action is recorded in the run log; there is no retry.
- After a server restart a rule continues from its last recorded transition: a condition that was already reported
  does not fire *enter* again.

### Versions

Changing the inputs, conditions or actions saves a **new version** of the rule: its state starts fresh, alarms it
raised are cleared, and the new version is evaluated at once — a condition that already holds fires without waiting
for the next value. Changing only the name or description keeps the version.

### Four eyes for rules that send commands

With *Four eyes for commands* on, a rule whose actions send device commands is saved as **pending approval**
(*rule saved — waiting for approval by a second person*) and is not evaluated. A different user with
`device.command` presses **Approve** or **Reject** with a reason. A rejected rule must be edited before it can be
enabled again. Editing an approved rule's actions or conditions sends it back to *pending approval*.

### Enable, disable, revoke

**Disable** and **Enable** ask for a reason; a disabled rule is not evaluated and its alarms are cleared. **Revoke**
ends the rule for good, clears its alarms and keeps it in the list as *revoked*.

## The run log

Below the rules, the run log lists the last 50 transitions:

| Column | Content |
|---|---|
| Time | when the rule fired |
| Rule | the rule and its version |
| Event | *enter* or *exit* |
| Inputs | the input values at that moment |
| Actions | one badge per action — green when done, red when it failed; hover for the detail |

## Converting a rule into a flow

*Flows → New flow → Start with: Converted automation rule* builds an equivalent draft flow from a rule — see
[Flows](flows.md). Stop the rule once the flow runs, so the actions do not happen twice.
