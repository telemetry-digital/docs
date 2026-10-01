---
title: Remote sites (camera relay)
slug: video-relay
sidebar_position: 6
tags: [video, relay, remote, nat]
---

Cameras at another site — a branch, a pumping station, a building without a VPN — do not need to be reachable from
the server. A **camera relay** at the site reads the local cameras and sends the video to the server over an
encrypted connection that **it opens itself**. Nothing is opened in the site's firewall and no VPN is needed there.

```
camera (192.168.1.64) --RTSP--> camera relay at the site ==RTSPS (TLS)==> server :8322 --> recording, live, walls
```

The relay runs on any computer at the site that reaches the cameras: Linux, a Raspberry Pi with a 64-bit system, or
Windows. It forwards the video unchanged (no decoding), so a Raspberry Pi relays many cameras. One relay forwards any
number of cameras; each stream reconnects on its own.

## 1. Prepare the server (once)

Set `[video] push_listen = ":8322"` in `config.toml` (with a TLS certificate — `push_tls_cert` and `push_tls_key`,
or the web server's certificate is used) and open **TCP 8322** in the server's firewall. A server in the internet
profile refuses to start the relay port without TLS.

## 2. Add the camera on the server

*Camera management → Add camera → Connection: The camera sends its video to the server*. After saving, the camera's
**secret and a ready `relay.toml` are shown once** — use *Download relay.toml*. Only an encrypted copy stays on the
server.

## 3. Install the relay at the site

Put the downloaded `relay.toml` on the computer at the site, fill in the camera's local address and run the
installer next to it.

Linux or Raspberry Pi:

```sh
curl -fsSL https://telemetry.digital/install-relay.sh -o install-relay.sh
sudo bash install-relay.sh --config relay.toml
```

Windows (PowerShell as administrator):

```powershell
irm https://telemetry.digital/install-relay.ps1 -OutFile install-relay.ps1; .\install-relay.ps1 -Config .\relay.toml
```

The installer downloads the program and checks it against `SHA256SUMS`, stores the configuration where only the
service can read it, **tests it**, and only then installs the service `ctrl32-camera-relay` (restarts on failure).
Delete the copied `relay.toml` afterwards.

| To | Run the installer with |
|---|---|
| add cameras | a new `relay.toml` |
| update the program | `--update` / `-Update` |
| test again | `--check` / `-Check` |
| remove it | `--uninstall` / `-Uninstall` |

## The configuration file

```toml
server = "rtsps://telemetry.example.com:8322"
web = "https://telemetry.example.com"            # the server's web address, for camera control

[[camera]]
id = "01a0dfcb-…"                                  # from the server
secret = "…"                                       # shown once
source = "rtsp://admin:pw@192.168.1.64:554/Streaming/Channels/101"
sub_source = "rtsp://admin:pw@192.168.1.64:554/Streaming/Channels/102"   # optional, for tiles
audio = true                                       # optional: forward the sound (AAC, Opus, G.711)
onvif = "http://192.168.1.64"                      # optional: PTZ, presets and events through the relay
```

Keep this file private: it holds the camera passwords and the secrets.

The test alone checks that the server is reachable and its certificate trusted, that every camera answers with
H.264, H.265 or MJPEG (and which sound), that the server accepts each secret, and — for cameras with `onvif` — that
the camera's ONVIF service and the server's control endpoint answer. Passwords and secrets are never printed.

## Camera control through the relay (PTZ, presets, events)

A camera behind a relay is controlled like any other: PTZ, presets, ONVIF events (motion, tamper, line crossing,
recording on events, flows) and *Fill in from ONVIF*. The relay carries the requests over the server's ordinary web
port (HTTPS) — no new port.

1. On the server, edit the relayed camera: **ONVIF address** = the camera's address *in its own network*, for example
   `http://192.168.0.21:2020/onvif/device_service` (TP-Link Tapo uses port 2020), and the camera's user name and
   password.
2. In `relay.toml` add `onvif = "http://192.168.0.21:2020"` to the camera (a `relay.toml` downloaded after the ONVIF
   address was set already has it) and run the installer again.
3. The camera page shows the PTZ button. While the relay does not poll, the page says *Camera control (PTZ, events) is
   not connected*.

**TP-Link Tapo behind a relay**: `source = "rtsp://USER:PASSWORD@IP:554/stream1"`, `sub_source = "…/stream2"`,
`audio = true` for the G.711 sound and `onvif = "http://IP:2020"` for PTZ, presets and motion events.

## Security

- The relay only sends; the server's relay port refuses reading.
- Each camera has its own 192-bit secret. *New secret* disconnects the old relay at once.
- With TLS the relay verifies the server's certificate. Without TLS (intranet only) the secret is checked with digest
  authentication and never crosses the network in clear text.
- Wrong secrets are logged as security events; an address with 10 failures in 10 minutes is blocked for 15 minutes.
- The relay never logs the secret or the camera password.
- The server can reach **only the camera's ONVIF service** through the relay — not the camera's web pages and nothing
  else in the remote network.

## Alternatives

- **WireGuard**: connect the site to the server's WireGuard network (*System → WireGuard*) and add the cameras
  normally — useful when the site already has a router with WireGuard.
- **Home Assistant add-on**: the relay is also available as a Home Assistant add-on, with the same per-camera options
  (`onvif`, `audio`, `web`).
- **A whole server at the site** reachable through a [Cloudflare Tunnel](../getting-started/cloudflare-tunnel.md),
  when the recordings must stay at the site.
