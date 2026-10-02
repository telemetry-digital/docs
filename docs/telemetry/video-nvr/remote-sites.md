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

The relay runs on any computer at the site that reaches the cameras: Linux, a Raspberry Pi with a 64-bit system,
Windows, or as a Home Assistant add-on. It forwards the video unchanged (no decoding), so a Raspberry Pi relays many
cameras. One relay forwards any number of cameras; each stream reconnects on its own with a back-off from 1 to
30 seconds.

## 1. Prepare the server (once)

Set the relay port in the `[video]` section of `config.toml` and open it (TCP) in the server's firewall:

| Key | Default | Meaning |
|---|:---:|---|
| `push_listen` | empty (off) | address the server accepts relayed cameras on, for example `":8322"` |
| `push_tls_cert` | `http.tls_cert` | PEM certificate for RTSPS |
| `push_tls_key` | `http.tls_key` | PEM key for RTSPS |
| `push_host` | host of `http.base_url` | host name the relays connect to; it goes into the generated `relay.toml` |

With a certificate the relays connect over **RTSPS** (TLS 1.2 or newer). Without one the port accepts plain RTSP with
digest authentication — for an intranet only. A server with the profile `internet` refuses to start with
`push_listen` but no TLS certificate.

While `push_listen` is not set, relayed cameras show the badge *receiving is off* in Camera management.

## 2. Add the camera on the server

*Camera management → Add camera → Connection: The camera sends its video to the server (remote site with
camera-relay)*. After saving, the dialog **Relay for the remote camera** shows the camera's **secret and a ready
`relay.toml` once** — *Download relay.toml* or *Copy*. Only an encrypted copy of the secret stays on the server; a
lost secret can only be replaced by a new one (*New secret* in Camera management, with a reason).

Switching an existing camera from *server connects* to *camera sends* also issues a secret; switching back removes
it.

![The Add camera dialog with the Connection field, which chooses whether the server connects to the camera or the camera sends its video to the server](img/add-camera.webp)

*The Connection field of the Add camera dialog.*

## 3. Install the relay at the site

Put the downloaded `relay.toml` on the computer at the site, fill in the camera's local address and run the
installer next to it.

Linux or Raspberry Pi (Debian, Ubuntu, Raspberry Pi OS with systemd):

```sh
curl -fsSL https://telemetry.digital/install-relay.sh -o install-relay.sh
sudo bash install-relay.sh --config relay.toml
```

Windows (PowerShell as administrator):

```powershell
irm https://telemetry.digital/install-relay.ps1 -OutFile install-relay.ps1; .\install-relay.ps1 -Config .\relay.toml
```

The installer downloads the program and checks it against `SHA256SUMS`, stores the configuration where only the
service can read it, **tests it**, and only then installs the service `ctrl32-camera-relay` (starts with the computer,
restarts on failure). Delete the copied `relay.toml` afterwards. Run it again to change the configuration.

| Linux | Windows | What it does |
|---|---|---|
| `--config FILE` | `-Config FILE` | the configuration to install (required on the first run) |
| `--binary PATH` | `-Binary PATH` | use a local program instead of downloading |
| `--url URL` | `-Url URL` | download address of the program |
| `--update` | `-Update` | download the current program again |
| `--force` | `-Force` | install even when the test fails (for example a camera is switched off for now) |
| `--check` | `-Check` | only test the installed configuration |
| `--uninstall` | `-Uninstall` | stop and remove the service; the configuration stays |
| `--purge` | `-Purge` | with uninstall: also remove the configuration (and on Linux the service user) |

| Item | Linux | Windows |
|---|---|---|
| Program | `/usr/local/bin/ctrl32-camera-relay` | `C:\Program Files\ctrl32-camera-relay` |
| Configuration | `/etc/ctrl32-camera-relay/relay.toml` (root:ctrl32relay, 0640) | `C:\ProgramData\ctrl32-camera-relay\relay.toml` (SYSTEM and Administrators only) |
| Service | systemd service `ctrl32-camera-relay`, user `ctrl32relay` (no shell, no home) | Windows service `ctrl32-camera-relay`, automatic start |

Windows 10/11 and Windows Server (x64) are supported. If PowerShell refuses to run the script, first run
`Set-ExecutionPolicy -Scope Process Bypass -Force` in the same window.

Without the installer the program runs as `ctrl32-telemetry camera-relay -config relay.toml` (add `-check` to test
only), and `ctrl32-telemetry service install-relay -config relay.toml` installs it as a service after checking the
configuration. On Linux the relay warns when `relay.toml` is readable by others (`chmod 600` it).

## The configuration file relay.toml

```toml
server = "rtsps://telemetry.example.com:8322"
web = "https://telemetry.example.com"            # the server's web address, for camera control
# ca_file = "/etc/ctrl32-camera-relay/ca.pem"    # only for a private certificate authority

[[camera]]
id = "01a0dfcb-…"                                  # from the server
secret = "…"                                       # shown once
source = "rtsp://admin:pw@192.168.1.64:554/Streaming/Channels/101"
sub_source = "rtsp://admin:pw@192.168.1.64:554/Streaming/Channels/102"   # optional, for tiles
transport = "auto"                                 # optional: auto, tcp or udp towards the camera
audio = true                                       # optional: forward the sound (AAC, Opus, G.711)
onvif = "http://192.168.1.64"                      # optional: PTZ, presets and events through the relay
```

### Top-level keys

| Key | Required | Default | Meaning |
|---|:---:|:---:|---|
| `server` | yes | — | `rtsps://host:port` of the server's relay port; `rtsp://` only inside a trusted network; port 8322 when omitted |
| `web` | with `onvif` | `https://<host of server>` | the server's web address, used for camera control |
| `ca_file` | no | system CAs | PEM file of a private certificate authority that signed the server's certificate |

### Keys of each [[camera]]

| Key | Required | Default | Meaning |
|---|:---:|:---:|---|
| `id` | yes | — | the camera's id from the server |
| `secret` | yes | — | the camera's secret (at least 16 characters; the server issues 32) |
| `source` | yes | — | `rtsp://` or `rtsps://` address of the camera's main stream, with its user name and password |
| `sub_source` | no | none | the camera's smaller stream for tiles |
| `transport` | no | auto | `auto`, `tcp` or `udp` towards the local camera |
| `audio` | no | false | forward the camera's sound (AAC, Opus or G.711; main stream only) |
| `onvif` | no | none | the camera's ONVIF address in this network, for example `http://192.168.0.21:2020`, or its `device_service` address |

Keep this file private: it holds the camera passwords and the secrets. Sound reaches the server only when both
`audio = true` here **and** *Sound* is switched on for the camera on the server.

## Test the relay

`--check` (or the test during installation) checks, and prints `ok` or `FAIL` for each step:

- the configuration is valid;
- the server is reachable and — with TLS — its certificate trusted;
- every camera answers with H.264, H.265 or MJPEG (and which sound, with `audio = true`);
- the server accepts each camera's secret;
- for cameras with `onvif`: the camera's ONVIF service and the server's control endpoint answer.

It ends with *the relay is ready*, or the number of failed checks. Passwords and secrets are never printed.

## Camera control through the relay (PTZ, presets, events)

A camera behind a relay is controlled like any other: PTZ, presets, ONVIF events (motion, tamper, line crossing,
recording on events, flows) and *Fill in from ONVIF*. The relay carries the requests over the server's ordinary web
port (HTTPS) — no new port.

1. On the server, edit the relayed camera: **ONVIF address** = the camera's address *in its own network*, in the form
   `http://CAMERA-IP:port/onvif/device_service` (TP-Link Tapo uses port 2020), and the camera's user name and
   password.
2. In `relay.toml` add `onvif = "http://192.168.0.21:2020"` to the camera (a `relay.toml` downloaded after the ONVIF
   address was set already has it) and run the installer again.
3. The camera page shows the PTZ button. While the relay does not poll, the page says *Camera control (PTZ, events) is
   not connected* and Camera management shows *relay control not connected*.

The relay polls the server for requests for up to 25 seconds at a time; a relay that has not polled for 35 seconds
counts as not connected. Each request and answer is limited to 1 MB.

**TP-Link Tapo behind a relay**: `source = "rtsp://USER:PASSWORD@IP:554/stream1"`, `sub_source = "…/stream2"`,
`audio = true` for the G.711 sound and `onvif = "http://IP:2020"` for PTZ, presets and motion events.

## Security

- The relay only sends; the server's relay port refuses reading.
- Each camera has its own 192-bit secret. *New secret* disconnects the old relay at once and is audited.
- With TLS the relay verifies the server's certificate. Without TLS (intranet only) the secret is checked with digest
  authentication and never crosses the network in clear text.
- Wrong secrets — on the relay port and on the control channel alike — are logged as security events; an address with
  **10 failures in 10 minutes is blocked for 15 minutes** (adjustable in [Video settings](settings.md)).
- The relay never logs the secret or the camera password.
- Through the relay the server can reach **only the camera's ONVIF service** at the origin named in `relay.toml` —
  paths under `/onvif/`, and elsewhere only SOAP calls — not the camera's web pages and nothing else in the remote
  network.

## Home Assistant add-on

The relay is also available as the Home Assistant add-on **ctrl32 camera relay** (aarch64, amd64, armv7) with the
same options: `server`, `web`, `ca_file`, and a list `cameras` with `name`, `id`, `secret`, `source`, `sub_source`,
`transport`, `audio` and `onvif`.

## Alternatives

- **WireGuard**: connect the site to the server's WireGuard network (*System → WireGuard*) and add the cameras
  normally — useful when the site already has a router with WireGuard.
- **A whole server at the site** reachable through a [Cloudflare Tunnel](../getting-started/cloudflare-tunnel.md),
  when the recordings must stay at the site.
