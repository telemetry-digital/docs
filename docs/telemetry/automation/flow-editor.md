---
title: The flow editor
slug: flow-editor
sidebar_position: 2
tags: [automation, flows, editor, debug]
---

Open a flow from *Flows* to edit it. The editor has five parts: the **header and toolbar** above, the **palette**
on the left, the **canvas** in the middle, the **property panel** on the right and the **debug panel** below.

![The flow editor: the node palette with triggers and logic on the left; on the canvas a Datastream value node wired through True for and Rate limit to Notify, Debug and Incident; the flow details on the right; the Debug panel below; Dry run, Versions, Export, Save, Deploy and Stop above](img/flow-editor.webp)

Anyone with `data.read` can open a flow and watch it; saving, deploying, stopping and sending test messages need
`content.write`.

## Header

| Element | Shows or does |
|---|---|
| ← | back to the flow list |
| Name | the flow's name |
| State badge | `running vN`, `stopped`, `draft`, or `subflow` |
| Meta line | the newest version, when it was saved and by whom |
| *unsaved* | appears as soon as the canvas differs from the newest saved version |

Leaving the page with unsaved changes asks for confirmation in the browser.

## Toolbar

| Button | Does |
|---|---|
| ↶ Undo | undoes the last change (Ctrl+Z); up to 100 steps |
| ↷ Redo | redoes (Ctrl+Y or Ctrl+Shift+Z) |
| − / + | zooms out or in by 20 %; the percentage shows the current zoom |
| ⤢ Fit | fits all nodes into the view (zoom between 30 % and 140 %) |
| Dry run | the default for test messages: show what actions would do instead of doing it; checked by default |
| Versions | the list of versions with Restore, see [Flows](flows.md) |
| Export | downloads the newest saved version as JSON |
| Save | asks for a reason and saves a new version; problems are allowed and counted in the message |
| Deploy | validates, asks for a reason, saves unsaved changes and runs the flow |
| Stop | only while the flow runs; asks for a reason and stops it |

In a [subflow](flow-subflows.md) *Deploy* and *Stop* are hidden — a subflow runs only inside the flows that use it.

![The dialog Save a new version: the running version keeps running until you deploy, and a field for the reason](img/flow-save-reason.webp)

## Palette

The palette lists the node library in groups: **Triggers**, **Logic**, **Actions** and **Other**. In a subflow the
group **Subflow** (*Subflow input*, *Subflow output*) replaces *Triggers*. Hover over a node to read its help.

- **Drag** a node onto the canvas to place it where you drop it.
- **Double-click** a node in the palette to place it in the middle of the view.
- **Filter nodes…** at the top keeps only the nodes whose name or type contains what you type.

New nodes snap to a 10-pixel grid, never land exactly on top of another node and get an identifier such as
`notify1`, `notify2`. Every setting starts with its default.

## Canvas

| To | Do |
|---|---|
| Select a node | click it |
| Add or remove a node from the selection | Shift-, Ctrl- or Cmd-click it |
| Select with a rectangle | Shift-drag on an empty spot |
| Select everything | Ctrl+A |
| Clear the selection | Esc, or click an empty spot |
| Move nodes | drag a selected node; all selected nodes move together and snap to the grid |
| Pan | drag an empty spot |
| Zoom | mouse wheel, around the pointer (25 % to 250 %) |
| Wire | drag from an output port (right side) to an input port (left side) or onto the target node |
| Select a wire | click it; the property panel offers *Delete wire* |
| Delete | Delete or Backspace removes the selected nodes with their wires, or the selected wire |
| Copy and paste | Ctrl+C and Ctrl+V; pasted nodes appear 40 pixels lower right with new identifiers, wires between them are kept |
| Send a test message | click ▶ to the left of a trigger node |

A wire is not drawn when the target has no input (a trigger), when it would connect a node to itself or when the
same wire already exists. Loops are allowed on the canvas but block deployment unless they pass a *Delay*,
*Debounce*, *Rate limit*, *Aggregate* or *True for* node.

### What a node shows

- The coloured bar and icon: the group — triggers teal, logic purple, actions amber, debug and comments grey.
- The name (yours or the node type) and a short summary of the main setting, for example the datastream, the
  condition and time, the channel or `every 60 s`.
- Port labels on nodes with several outputs: *true* / *false*, *inside* / *outside*, *became true* /
  *became false*, and `1`, `2`, … `else` on a *Switch*.
- **Counters** under a node of a running flow: `in N · out N`, and `errors N` in red when the node failed.
- A **red** node has a problem; hover over it or select it to read the problem.
- A faded node is **disabled**.

## Property panel

With **nothing selected**, the panel shows the flow: the number of nodes, whether it runs and which version, the
number of dropped messages, and the names and functions of the expression language.

With **one node selected**:

| Part | Content |
|---|---|
| Head | the icon, the node type and the node's identifier |
| Help | what the node does |
| Problems | the node's validation problems, if any |
| Name | your name for the node, up to 64 characters; empty shows the node type |
| Settings | one field per setting, marked `*` when required — see the node pages |
| Disabled | the node stops processing; disabled triggers never fire |
| URL | *Incoming webhook* only: the address to call (after saving) |

Every change is applied at once and can be undone. Field types:

| Setting kind | Field |
|---|---|
| Text, URL | a text box |
| Number | a number box with the allowed range |
| Yes/no | a check box |
| Choice | a drop-down; an empty option is shown as *(any)* |
| Several choices | a check box per option |
| Expression | a monospaced text box — see [Flow expressions](flow-expressions.md) |
| Template | a monospaced text area with `{{ expression }}` placeholders |
| Time | a time picker (HH:MM) |
| Datastream, device, channel, entity, scene, camera, MQTT account or bridge, subflow | a drop-down of the items of your organization |
| Rules (*Switch*) | a text area, one condition per line |
| Changes (*Change*) | rows of operation, field and expression or new name, with *Add change* and ✕ |

With **several nodes selected** the panel offers **Delete**, **Copy** and **Group**. With a **group** selected it
offers the group's name, colour and *Delete group* — see [Subflows and groups](flow-subflows.md).

## Test messages

Click ▶ on a trigger node. The dialog *Test message* has:

| Field | Meaning |
|---|---|
| Value | a number, text, `true`, `false` or `null`; empty sends the node's own value (for *Manual inject* its value expression, otherwise null) |
| Topic | empty uses the node's name |
| Dry run | show what the actions would do, do not execute them; preset from the toolbar |

Where the message goes depends on the flow:

- **Unsaved changes or not running**: the edited canvas is checked, then runs once as a temporary copy for up to
  30 seconds. Everything is a dry run, whatever you chose. A canvas with problems is refused and the problems are
  marked.
- **Saved and running**: the message enters the running flow. Without *Dry run*, actions really happen; for a flow
  that sends commands this needs `device.command`, and the test is audited as `flow.inject`.

## Debug panel

The debug panel below the canvas shows the flow's debug entries, refreshed every 1.5 seconds; node counters refresh
every 3 seconds.

| Kind | Written when |
|---|---|
| debug | a *Debug* node shows a message or an expression |
| action | an action node finished, for example `notified: …`, `command write {…}` |
| dry action | an action did not run because of a dry run — the text says what it would have done |
| error | a node failed; the message that failed is attached |
| drop | a *Rate limit* dropped a message, or the flow's queue overflowed |
| info | the flow started (with its version), a test message was sent, a *Split* cut a list, an *MQTT in* could not subscribe |

Each row shows the time, the node, the kind (marked *dry* for dry runs) and the text. *message* opens the full
message as JSON. **Click a row** to select its node on the canvas. **Only the selected node** hides the other rows;
**Clear** empties the view (the server keeps its entries).

The server keeps the last **500 entries per flow** in memory; they are lost when the server restarts. Nodes inside
a subflow appear as `<subflow node>.<node>`, for example `sub1.notify1`.
