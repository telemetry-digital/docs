---
title: Evidence
slug: video-evidence
sidebar_position: 9
tags: [video, evidence, export, signature]
---

When a recording is needed by the police, an insurer or a court, it must not disappear with the retention and it
must be possible to prove that nobody changed it. Evidence actions are **behind their own permission** —
`video.evidence` (engineers and administrators) — need a **reason**, and are **audited**.

## Hold a recording as evidence

On the camera's page drag over the hour detail to select a part and choose **Hold as evidence**. You are asked for:

| Field | Limits | Meaning |
|---|---|---|
| Title | 1–120 characters, required | what happened |
| Case or report number | ≤ 80 characters, optional | the police or insurance reference |
| Reason | required | written to the hold and to the audit trail |

The server accepts holds of up to 31 days and ending at most one day ahead (through the API); in the hour detail the
selection is at most one hour.

- Held recordings are **never deleted** — not after the camera's retention days and not to free disk space. Events
  inside a held range are kept too. When only held recordings remain on a full disk, the disk incident tells
  administrators.
- Held ranges are drawn on the camera's timeline and listed below it.
- **Release** (with a reason) hands them back to the normal retention, which may delete them at once if they are
  older than the camera's retention.

![A selected range of about nine minutes in the hour detail with the buttons Download selection (MP4), Hold as evidence and Evidence package (ZIP)](img/export-selection.webp)

## The Evidence page

*Cameras → Evidence* (needs `video.playback`) shows:

- the **server key for verifying evidence packages**: its **fingerprint** (32 hexadecimal digits in groups of four)
  and, folded, the public key;
- **Held recordings**: title, case number and reason; camera; time; size; who held it and when; state *held* or
  *released* (with who released it and why). **show released** includes released holds. At most the 500 newest are
  listed; a user limited to camera groups sees only their cameras.

![The Evidence page: the fingerprint of the server key for verifying evidence packages, the public key and the list of held recordings](img/evidence.webp)

*Cameras → Evidence with the key fingerprint and the held recordings.*

## Fingerprints while recording

Every recorded file gets its **SHA-256** computed while it is written, without extra disk reads. An evidence package
therefore shows that the files on disk are unchanged since they were recorded.

## Evidence package

Select at most one hour in the hour detail and choose **Evidence package (ZIP)**. You are asked for a reason (up to
500 characters, written into the signed manifest and the audit trail) and an optional case or report number (up to
80 characters). The package is built on the server first, so the audit entry carries the video's hash before anything
leaves the server. It is named `evidence-<camera>-<start time, UTC>.zip`.

| File | Content |
|---|---|
| `video.mp4` | the video, remuxed but never re-encoded — the pictures are the camera's own bytes |
| `manifest.json` | format `ctrl32-evidence/1`: server address and version, organization, camera (id, name, location), time range, when, by whom (user name, full name, id) and why it was exported, case number, the SHA-256 of `video.mp4` and of each recorded file as computed while recording and as found now, the events in the range (up to 1000) |
| `manifest.sig` | Ed25519 signature of `manifest.json` by the server's key |
| `public_key.txt` | the server's public key and its fingerprint |
| `README.txt` | how to verify the package |

Sound is included only when the camera's sound is on and you have `video.audio`; a masked camera can be exported only
with `video.unmask`.

## Verify a package

Anyone can verify a package with the server program:

```sh
ctrl32-telemetry verify-evidence -key <public key in base64> package.zip
```

| Option | Meaning |
|---|---|
| `-key` | the server's public key (base64, from *Cameras → Evidence*); without it the key in the package is used and you compare its fingerprint |

The command prints the camera, the recorded and exported times, who exported it and why, the key fingerprint,
*signature valid/INVALID*, *video hash valid/INVALID* and the number of recorded files, followed by any notes. It ends
with *the package is intact* or *the package is NOT intact*. **Any changed byte of the video or of the manifest is
detected.**

By hand: `sha256sum video.mp4` must equal `video.sha256` in `manifest.json`, and the signature in `manifest.sig` must
verify against the public key.

The key fingerprint must equal the one shown under *Cameras → Evidence* — hand it to the recipient through another
channel than the package itself.

!!! warning "Keep the server's secret key in your backup"
    The server's signing key is created on first use and stored encrypted with the server's secret key
    (`security.secret_key` in `config.toml`). Keep that secret in your backup. Without it, old packages can still be
    verified with the published public key, but the stored signing key cannot be opened and packages cannot be
    signed.
