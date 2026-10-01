---
title: Apps for phones and tablets (PWA)
slug: pwa-apps
sidebar_position: 5
tags: [administration, pwa, apps, phone]
---

The web interface installs as an app: *Add to Home Screen* on a phone or tablet, *Install app* in Chrome or Edge on a
computer. Nothing of the data is stored on the device — the app caches only its own shell; every page and every
value comes from the server.

## The main app

Installing from the main address gives you the whole application, with shortcuts to Cameras, Video walls and Home.
With [white labeling](white-labeling.md) it carries your product name and icon.

## Your own apps

*Settings → Apps* defines more installable apps over the same application — for example a phone app that opens the
live cameras and shows only the Cameras menu, a tablet at the gate that opens one video wall, or a smart-home app with
Home and Energy.

| Setting | Meaning |
|---|---|
| Name | shown on the install page and in the app's header |
| Name on the home screen | the label under the icon (up to 12 characters fit on most phones) |
| Address | the app lives at `/app/<address>` |
| Start page | what opens first: a dashboard, the camera live view, a video wall, one camera, Home, Energy, Incidents, Displays, Devices, Flows, Assets … |
| Menu sections | the menu groups the app shows; *My account* is always there |
| Own colour | the title bar colour |
| Enabled | a disabled app is no longer found; installed icons open a "not found" page |

The create dialog has **presets**: *Cameras*, *Video wall*, *Dashboards* and *Smart home*. Every change needs a reason
and is audited. Managing apps needs `content.write`.

## Options of the app window

| Option | Default | Effect |
|---|---|---|
| Locked to its sections | off | no way into the full application; a page outside the chosen sections opens the start page instead |
| Zoom the page with two fingers | on | off: the page does not zoom, but the camera picture still zooms inside the player |
| Cameras in live view and on video walls | automatic | one camera per row on narrow screens and a grid elsewhere, or always one of them |

The **Cameras** preset turns all three into a camera-only phone app.

## Installing an app

Open `https://<your server>/app/<address>` on the device. On a computer the page shows a **QR code** to scan with the
phone.

- **Android** (Chrome, Edge, Samsung Internet) and **computers** (Chrome, Edge): *Install*, or the browser menu →
  *Install app* / *Add to Home screen*.
- **iPhone and iPad** (Safari): *Share* → *Add to Home Screen* → *Add*.

Several apps and the main app can be installed side by side.

!!! warning "An app is a view, not a permission boundary"
    The menu of an app is filtered by the user's permissions first, and the server checks permissions on every
    request. Locking an app limits its screens, not the user's rights. To limit what a person can see, use roles and
    camera groups.

The install page and the app's manifest are public (a phone must be able to install the app before signing in); they
contain only the app's name, start page, sections and colour — no data of the organization.
