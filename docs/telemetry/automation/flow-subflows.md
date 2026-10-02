---
title: Subflows and groups
slug: flow-subflows
sidebar_position: 7
tags: [automation, flows, subflows, groups]
---

## Subflows

A **subflow** is a reusable part of a flow — for example *notify the shift and open an incident* — built once and
used in many flows as one **Subflow** node. Change it in one place; each flow takes the change when you deploy it
again.

### Create a subflow

1. **Flows → New flow**, name it, choose *Start with: Subflow (a reusable part used inside flows)* and give a reason.
2. The canvas starts with a **Subflow input** and a **Subflow output** 1. The palette shows the group *Subflow*
   instead of *Triggers*.
3. Wire the logic and actions between them and **Save** with a reason. A subflow has no *Deploy* — it runs only
   inside the flows that use it.

| Node | Setting | Default | Allowed | Rule |
|---|---|:---:|:---:|---|
| Subflow input | — | — | — | exactly one per subflow; messages enter here |
| Subflow output | Output | 1 | 1–4 | any number of them; the number is the output of the *Subflow* node where the message leaves |

A subflow cannot contain triggers, and *Subflow input* and *Subflow output* cannot be used in an ordinary flow.
There are no settings per use: the subflow reads the message, so set the fields it needs with a *Change* node in
front of it.

### Use a subflow

Add a **Subflow** node (group *Logic*) and choose the subflow. The node gets as many outputs as the subflow's
highest output number among its enabled outputs.

| Setting | Type | Default | Notes |
|---|---|:---:|---|
| Subflow | subflow | — | required; subflows of your organization, not the one you are editing |
| Version (empty = latest) | number | — | when you save the flow, an empty version is filled in with the subflow's current version number |

On the canvas the node shows the subflow's name and `vN`.

### Versions are pinned

The version is **pinned** when the flow is saved. Changing the subflow later does not change any running flow. To
take a new subflow version into a flow:

1. Open the flow, select the *Subflow* node and clear *Version* (or choose the subflow again).
2. Save — the newest subflow version is pinned.
3. Deploy with a reason. The deploy is audited like any other; when the subflow sends commands, the flow is a
   commanding flow and the four-eyes policy for commands applies.

This keeps restarts reproducible: a flow always runs exactly the subflow versions it was deployed with.

### How a subflow runs

Before a flow starts — and for test messages of an edited flow — every *Subflow* node is replaced by a copy of the
subflow's nodes. The copies run inside the flow, with its queue, limits, counters and debug panel. Their identifiers
are `<subflow node>.<node>`, for example `sub1.notify1`, in the debug panel and in the audit trail.

| Rule | Value |
|---|---|
| Subflows inside subflows | up to 4 levels |
| A subflow that uses itself, directly or through another | refused |
| Commands anywhere inside (also nested) | make the using flow a commanding flow |
| Deploying a subflow | refused: *a subflow runs inside flows and is not deployed itself* |

A disabled *Subflow* node is not expanded; messages wired into it stop there.

### Example

A subflow *Double and filter* with one input, a *Math* node `value * 2` and two outputs: output 1 gets every
result, output 2 only results above 10 (behind a *Condition* `value > 10`). In a flow, 7 leaves on both outputs as
14; 3 leaves only on output 1 as 6.

## Groups

**Groups** frame nodes in a named, coloured rectangle to keep the canvas readable. They do not change what the flow
does.

1. Select the nodes (Shift-drag a rectangle, or Shift-click them).
2. Press **Group** in the property panel. The group is drawn around the nodes with the name *Group N* and the next
   colour.
3. With the group selected, the property panel offers its **Name** (up to 64 characters), six **colours** and
   **Delete group**.

| To | Do |
|---|---|
| Move a group with its nodes | drag the group's background; the nodes fully inside it move along |
| Resize | drag the small square in the lower right corner (at least 120 × 60) |
| Delete the group | *Delete group*, or Delete / Backspace while the group is selected; the nodes stay |

A flow can have up to 50 groups. Groups are saved with the flow's version and exported with it.
