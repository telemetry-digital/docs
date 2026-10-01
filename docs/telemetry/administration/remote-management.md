---
title: Remote management of PCs
slug: remote-management
sidebar_position: 7
tags: [administration, remote-management, meshcentral]
---

telemetry.digital manages **its own** settings remotely — through the web interface, the API and the Server page
(updates, backups, restarts). It deliberately **does not control the operating system** of the PC it runs on: its
privileged agent performs only a closed list of operations.

Remote desktop, a terminal, files and operating-system updates of the PCs at customers' sites are the job of a
separate tool running next to it. For this we use **MeshCentral** (open source, Apache-2.0), self-hosted:

- On each managed PC runs the small MeshCentral agent (a Windows service or a Linux daemon). It connects **out** to
  the MeshCentral server over HTTPS — no open port and no VPN at the customer's site.
- Remote desktop in the browser, terminal (PowerShell, cmd, bash), file transfer, processes, services, event log and
  Wake-on-LAN.
- Every remote session is in the server's event log: who, which PC, when.

## Adding a customer's PC

1. MeshCentral → *My Devices* → *Add Device Group* (for example the customer's name), type *Manage using a software
   agent*.
2. *Add Agent* → Windows or Linux → download the installer (it carries the server address and the group) and run it on
   the customer's PC as administrator → *Install*.
3. The PC appears in the group; *Desktop*, *Terminal* and *Files* work at once. Set the group's *User Consent* if the
   customer wants to confirm every remote session.

## Security

- Only accounts created by an administrator; two-factor sign-in required; 10 failed sign-ins in 10 minutes lock the
  address for 30 minutes.
- Remove a PC from its group when the contract ends; the agent then has no access.

!!! note "Your own MeshCentral"
    If you run your own telemetry.digital server on Linux, the same setup can run next to it: MeshCentral listens on
    the machine itself and the server's HTTPS front end serves it under its own host name. The host name must be a
    DNS-only record (not proxied by a CDN), because the agents check the server's own certificate.
