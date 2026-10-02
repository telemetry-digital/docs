---
title: Access from the internet with a Cloudflare Tunnel
slug: cloudflare-tunnel
sidebar_position: 5
tags: [getting-started, cloudflare, networking]
---

A server at a customer's site — next to the cameras, with the recordings staying there — can be reachable from
anywhere, the phone app included, at an address such as `https://cameras.example.com`, **without opening a port,
without a public IP address and without a certificate**. The small program `cloudflared` connects out to Cloudflare;
Cloudflare terminates HTTPS and forwards the requests to the server on the machine itself.

Sign-in and live camera video on a phone have been verified through a tunnel.

![A camera page on a phone: the live picture, the player controls and the day's timeline of recordings](img/phone-camera.webp)

*A live camera on a phone, as it is reached through a tunnel.*

## 1. In Cloudflare (once per site)

1. The customer's domain is on Cloudflare, for example `example.com`.
2. *Zero Trust → Networks → Tunnels → Create a tunnel → Cloudflared*, name it, for example `site-cameras`.
3. Copy the **token** from the install command Cloudflare shows (the long string after `service install`).
4. *Public hostname*: subdomain `cameras`, domain `example.com`, service **HTTP** `localhost:8080`.
5. Optional: *Access → Applications* puts an extra Cloudflare sign-in in front (for example only the customer's
   e-mail addresses).

## 2. On the PC at the site

Windows (elevated PowerShell):

```powershell
Set-ExecutionPolicy -Scope Process Bypass -Force
irm https://telemetry.digital/install.ps1 -OutFile install.ps1
.\install.ps1 -Domain cameras.example.com -CloudflareToken <token> -OrgName "Customer Ltd"
```

Linux (Debian, Ubuntu, Raspberry Pi OS 64-bit):

```bash
curl -fsSL https://telemetry.digital/install.sh | sudo bash -s -- --domain cameras.example.com --cloudflare-token <token>
```

The installer sets the **internet** profile (two-factor sign-in for every user), binds the web server to
`127.0.0.1:8080`, opens **no** firewall port and installs `cloudflared` as a service. The one-time password of
`admin` is in `admin-bootstrap.txt` (see [First sign-in](first-sign-in.md)).

## 3. Cameras and the phone app

1. Sign in at `https://cameras.example.com`, set up two-factor sign-in and add the cameras
   (*Cameras → Camera management*; *Find cameras* scans the local network).
2. *Settings → Apps → New app → Cameras* creates a phone app at `https://cameras.example.com/app/cameras`. Open it on
   the phone and install it, or scan the QR code on its install page. See [Phone app](../video-nvr/phone-app.md).
3. Give each person of the customer their own account under *Users*, with camera groups if needed.

## Good to know

- **MQTT** listens on the machine itself only. Devices in the customer's network that send over MQTT need MQTT over
  TLS (`mqtt.tls_listen` with a certificate), or an installation in the intranet profile without a tunnel.
- **Video through Cloudflare**: Cloudflare's terms have limited serving large amounts of video in the past. Check the current terms for the number of viewers you expect. A few people watching live cameras is
  typical use; recordings are downloaded as MP4 exports.
- **Changing the address later**: change the public hostname in Cloudflare, then `http.base_url` in `config.toml`,
  and restart.
- **An existing installation** switches to a tunnel when you run the installer again with the token; it keeps the
  existing `config.toml` and prints the lines to change.
- **No server at the site?** Install only the camera relay there and send the video to a central server — see
  [Remote sites](../video-nvr/remote-sites.md).
- Remote administration of the PC itself (desktop, files, operating-system updates) is not part of
  telemetry.digital; see [Remote management](../administration/remote-management.md).
