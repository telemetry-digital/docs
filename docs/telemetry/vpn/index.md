---
title: VPN
slug: vpn
sidebar_position: 9
tags: [vpn, wireguard, remote-access, site-to-site, security]
---

**System → VPN** turns your telemetry.digital server into a WireGuard hub with **access rules**. Technicians reach
PLCs and HMIs at your sites, routers connect whole site networks, and devices send data through the tunnel — and
every one of them reaches only what a rule allows.

![System → VPN with four eyes for VPN access on: the server card with state, port, address, endpoint and firewall, the licence card, the peers (a site router, technicians — one awaiting approval, one with an approved access until midnight — and a gateway) and access rules, with the card Uplink to a hub of this server and a customer server connected as a Server (uplink) peer](img/vpn-page.webp)

## What it is for

| Goal | How |
|---|---|
| A technician reaches a PLC or HMI at a site — TIA Portal, Modbus TCP, a web HMI, VNC | a **technician** peer (a laptop or phone with the WireGuard app) and a rule *technicians → site host : ports* |
| A site's whole network is connected (site-to-site) | a **site** peer: a router (OpenWrt, MikroTik, Teltonika, Ubiquiti, pfSense, OPNsense, Linux) or a Raspberry Pi, with the LAN behind it as routed subnets |
| Devices send data without TLS | a **device** peer and a rule *devices → this server : MQTT 1883* |
| A company VPN for your own people | technician peers and rules towards the hosts they need |
| A contractor needs access for one night shift | an **access grant** of 8 hours, approved by a second person when four eyes is on |
| Each customer manages its own sites | the administrator of an organization manages the organization's peers and rules under **Settings → VPN** |
| The server reads a PLC at a site (OPC UA, Modbus connectors) | nothing to add on a Linux server: the server itself may always reach its peers and the site networks |
| A telemetry.digital server in a customer's LAN without a public address is serviced remotely | an **uplink** to a hub with a public address, for example your server in the cloud — rules on the hub and on the customer server (see [Uplink to a hub](uplink-hub.md)) |

## How it works

- **Hub and spoke.** The server is the hub; every peer connects to it over UDP (port 51820 by default). Peers do not
  connect to each other directly, and traffic between them passes the server's firewall.
- **Default deny.** Everything that arrives from the VPN is **dropped unless an access rule allows it** — also
  traffic to the server's own services (the web interface, MQTT, SSH). A new peer reaches nothing until a rule
  allows it.
- **One configuration, shown once.** Each peer gets its own key pair. The configuration with the private key is
  shown once — as a QR code, a `.conf` file and, for sites, router texts — and is never stored on the server.
- **Enforced by the server agent.** On Linux the agent turns the peers and rules into WireGuard settings and an
  nftables firewall table, checks them and loads them in one step; a failed change restores the previous state. On
  Windows the agent runs WireGuard itself and checks every packet against the same rules (see
  [Windows servers](setup.md#windows-servers)).

## Free and with a licence

| Capability | Free | With a licence |
|---|:---:|:---:|
| VPN server, access rules, firewall rules view | yes | yes |
| Technician and device peers | up to 5 | unlimited |
| Site peers with routed subnets (site-to-site) | — | yes |
| Customer servers connected by uplink (a Server (uplink) peer on the hub) | — | yes |
| Time-limited access (an expiry date or an access grant) | — | yes |
| Technician access approved by a second person (four eyes) | — | yes |

Up to 5 peers are free; more peers, sites and time-limited or approved technician access require a licence (the same licence as
white labeling, see [Licence](../licence/index.md)). Without the licence existing peers keep working and access rules
can always be changed — restricting access never needs a licence. Adding a peer beyond the limit, a site or an
expiry is refused with a message that says why.

## Safety first

- The VPN is **off** until a server administrator enables it. Server administrators (`system.admin`) manage
  everything; the administrator of an organization (`vpn.manage`) manages only the organization's own peers and
  rules.
- Technicians' access can require the **approval of a second person** and can be limited to a time window.
- Every change asks for a **reason** and is written to the [audit trail](../administration/audit.md).
- Showing a configuration — adding a peer or rotating its key — asks for **your password again** (and the
  authenticator code with two-factor sign-in). AI assistants are never offered these two operations.
- **Disable** ends a peer's access at once; time-limited access ends by itself.

!!! warning "The server becomes a gateway into your sites"
    With site peers, whoever controls the server or its access rules can reach the machines behind the site routers.
    Keep rules narrow (one host, the ports it needs), give technicians time-limited access, protect administrator
    accounts with two-factor sign-in, and have the setup reviewed before production machines are reachable through
    it.

## Pages in this section

1. [Set up the VPN](setup.md) — requirements, enabling the server, the VPN page at a glance.
2. [Peers](peers.md) — technicians, devices and sites, every field, the configuration shown once, key rotation,
   expiry and router configurations.
3. [Access rules](access-rules.md) — sources, destinations, protocols and ports, examples for PLCs and HMIs, and the
   generated firewall rules.
4. [Organizations, access grants and approvals](organizations-and-approvals.md) — the VPN of an organization,
   time-limited access, four eyes for technicians.
5. [Security, upgrade and troubleshooting](troubleshooting.md) — what is blocked, the audit trail, moving from the
   earlier WireGuard page, limits and checks on the server.
6. [Uplink to a hub](uplink-hub.md) — a server without a public address connects out to a hub; technicians reach it
   and its LAN as the rules on both sides allow; the HTTPS transport for networks that block UDP.

## Requirements

- A server with the server agent, installed by the installer: **Linux** (Debian or Ubuntu) or **Windows**. On
  Windows a few things work differently — see [Windows servers](setup.md#windows-servers).
- A UDP port the peers can reach — open it in your provider's or your router's firewall too. A server without one
  joins a hub through an [uplink](uplink-hub.md).
- IPv4.
