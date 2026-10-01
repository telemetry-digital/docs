---
title: Recording and storage
slug: video-recording
sidebar_position: 2
tags: [video, recording, storage, sizing]
---

Each camera records **continuously**, **on events**, or not at all. Recordings are MP4 files of about one minute,
each starting with a key frame, stored per camera and day and indexed in the database.

## Recording modes

- **Continuous** — everything is recorded. Events are stored too, as marks on the timeline.
- **On events** — only around the chosen kinds of events (motion, tamper, line crossing, intrusion, digital inputs,
  marks): a **pre-roll** of 0–60 seconds kept in memory and a **post-roll** of 1–600 seconds after the event ends.
  See [Events and motion](events-and-motion.md).
- **Off** — live view only.

**Playback never stops recording.** Recording runs on the server by itself; watching, pausing or downloading only
reads the files.

## Retention and disk limit

- **Keep recordings (days)** per camera.
- **Disk space for recordings** for all cameras together (*Cameras → Settings*, default 50 GB from `config.toml`).
  Beyond it the oldest recordings are deleted.

Retention runs every minute: first each camera's days, then the disk limit. Removing a camera keeps its recordings
until its retention ends. Recordings **held as evidence** are never deleted — see [Evidence](evidence.md).

Where recordings are stored is set in `config.toml`:

```toml
[video]
storage_dir = "/var/lib/ctrl32-telemetry/video"
max_disk_gb = 50
```

## How much disk you need

Storage per day ≈ cameras × Mbit/s × 10.8 GB. One 2 Mbit/s camera needs about 21 GB a day.

| 64 cameras | Network | Disk per day | 14 days |
|---|:---:|:---:|:---:|
| H.265, 2 Mbit/s | 128 Mbit/s | ≈ 1.4 TB | ≈ 19 TB |
| H.264, 4 Mbit/s | 256 Mbit/s | ≈ 2.8 TB | ≈ 39 TB |

Sound adds to it, per camera and day: G.711 about 1.4 GB (it is stored uncompressed), AAC 0.2–0.7 GB.

## Disks

- Put the recordings on **their own disk or RAID of surveillance hard drives** (rated for continuous writing); keep
  the database on an SSD. SSDs wear out quickly under continuous recording.
- Files are written in large sequential pieces, about one second per camera, and the index is updated every
  10 seconds per camera, so the database load stays negligible.
- An incident opens when the video disk is nearly full (default: below 5 % free) — see [Video settings](settings.md).

## How many cameras one server takes

The server never decodes video, so processor load grows with the number of packets, not with resolution. Measured on
one virtual machine:

| Load | Result |
|---|---|
| 64 H.264 cameras at 25 fps, 297 Mbit/s, all recording (4-core VM) | all online, about 60 % of one core, 91 MB of memory; with 40 live viewers about 80 % of one core, every viewer at the full 25 fps |
| 128 cameras, 593 Mbit/s, all recording | all online, about 70 % of one core, 153 MB; with 40 live viewers about 110 % of one core, every viewer at the full 25 fps |

For 128 cameras plan a 1 Gbit network (a 10 Gbit uplink when the cameras run 4 Mbit/s or more) and a RAID of
surveillance drives that sustains the total write rate.

!!! note "Many cameras over UDP on Linux"
    The server asks Linux for a 4 MB receive buffer when allowed. With the common default of 208 KB, packets of large
    key frames can be dropped. For many or high-resolution cameras over UDP set `net.core.rmem_max=4194304` (and keep
    it in `/etc/sysctl.d/`), or switch the cameras to transport TCP.
