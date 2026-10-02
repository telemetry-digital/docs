---
title: Phone app
slug: video-phone-app
sidebar_position: 7
tags: [video, pwa, phone, app]
---

The web interface installs on a phone or tablet as an app (a PWA). For cameras the best choice is an **own app that
shows only the cameras** — the person opens it and sees the live view, nothing else.

## Create a camera app

1. *Settings → Apps → New app*, preset **Cameras** (start page: live view, menu: Cameras) or **Video wall** (start
   page: one wall).
2. Give it an address, for example `cameras`, and a reason. The app lives at `https://<your server>/app/cameras`.

![The New app dialog with the Cameras preset: name Cameras, address /app/cameras, start page Cameras — live view, only the Cameras menu section, locked to its sections, page zoom off and one camera per row](img/new-camera-app.webp)

The **Cameras** preset also locks the app to its sections, turns off page zoom (the camera picture still zooms with
two fingers inside the player) and shows one camera per row on narrow screens. See [PWA apps](../administration/pwa-apps.md)
for all options.

## Install it on the phone

Open `https://<your server>/app/cameras` on the phone — on a computer the install page shows a QR code to scan.

- **Android** (Chrome, Edge, Samsung Internet): tap *Install*, or browser menu → *Install app* / *Add to Home screen*.
- **iPhone and iPad** (Safari): *Share* (the square with the arrow) → *Add to Home Screen* → *Add*.

## What it does

- Live video, recordings with the same controls as in the browser (pinch to zoom) and video walls.
- Live video on iPhone needs iOS 17.1 or later; MJPEG cameras work everywhere.
- **Nothing of the video or the data is stored on the phone** — the app caches only its own shell.
- Access from outside goes through the same HTTPS address as the web interface, with the same sign-in, two-factor
  sign-in and permissions. For a server without a public address, see
  [Cloudflare Tunnel](../getting-started/cloudflare-tunnel.md).

![The live view on a phone: one camera per row, two tiles marked with a motion label](img/phone-live.webp)

*The live view on a phone; tiles with an event carry a label.*

![A camera page on a phone: the live picture, the control bar, the stream information and the day's timeline](img/phone-camera.webp)

*The camera page on a phone.*

!!! warning "An app is a view, not a permission boundary"
    The app shows only the cameras a person may see, because the server checks permissions on every request. Locking
    an app to the Cameras menu does not take away rights the person's role has. To limit what someone can see, use
    roles and camera groups — see [Privacy, sound and permissions](privacy.md).
