---
title: Licence
slug: licence
sidebar_position: 9
tags: [licence]
---

telemetry.digital runs without a licence. A few features require one, installed on the server under
**System → License**.

## Features that require a licence

| Feature | Without a licence | With a licence |
|---|---|---|
| Cameras | a limited number of cameras | more cameras on the server |
| [White labeling](../administration/white-labeling.md) | the product footer (ctrl32 · telemetry) and product name stay | your product name, footer, icon and app name everywhere |
| [Changes through AI assistants](../ai-assistants/index.md) | assistants can only read | assistants can make changes within the user's permissions |

Everything else — devices, dashboards, process screens, recording, flows, alarms, reports, the API — works without
a licence. Your logo, colours and theme (branding) need no licence either.

## Activate a licence

1. Check that `http.base_url` in `config.toml` holds the name the server is reached at, for example
   `https://scada.example.com`. A licence is issued for that host name.
2. **System → License → Activate with the order number**: enter the order number and a reason, then *Activate*. The
   server sends the order number and its host name to the licence service, checks the licence it receives (signature
   and server name) and installs it.
3. The License page shows the licensee, the validity and the stored order number. The activation is written to the
   audit trail.

Licences are checked on every use, so installing or removing one takes effect at once.

## A server without internet access

A server that cannot reach the licence service gets a **licence key** instead — one line of text. Ask for it with the
order number and the server's host name, then **System → License → Install a licence key (offline)**: paste the key,
give a reason, *Install*. The key is verified offline.

## When there is a problem

- **A refused order** (for example an unknown or already used order) is shown on the License page with the reason.
- **A temporary problem** (no internet, the service busy) is retried automatically.
- **Removing the licence** (*System → License → Remove license*) brings back the product footer and name, and
  assistants become read-only again. Your white-label settings stay and apply again once a licence is installed.

## The software licence

The software is licensed under the **Apache License 2.0 with additional conditions**: the software itself may not be
resold or offered as a paid hosted service, and the product footer (ctrl32 · telemetry, with the links to the source
and to the licence) must stay visible and unchanged unless the deployment has a valid white-label licence. Charging
for your own implementation, integration, training or support services is not restricted.

The full text is on the `/license` page of every running server.

!!! warning "No certification"
    The licence gives you the software as it is. telemetry.digital carries no certification and is not meant for
    safety functions — see the warning on the [overview page](../index.md).
