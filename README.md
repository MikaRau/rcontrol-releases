# rcontrol-releases

Signed firmware for the RControl air-suspension controller, and a manifest
saying which build is current. The source lives in a private repository; this
one exists so the app has a fixed address to ask.

## What is here

- **`manifest.json`** — the address the app checks. It says which version is
  current and where the image for it is. Served from
  `https://raw.githubusercontent.com/MikaRau/rcontrol-releases/main/manifest.json`,
  which is the one string built into the app.
- **Releases** — one per firmware version, each holding a single
  `rcontrol-<version>.signed.bin`.

## Nothing here is what makes an image safe

Worth saying plainly, because a repository of firmware invites the opposite
assumption. The controller runs MCUboot with a project ECDSA-P256 key and
refuses anything not signed with it, whatever served it. A stolen account, a
hostile mirror and a man in the middle on a rooted phone all reduce to the same
capability: **serving an old genuine image**.

Which is why the version that decides whether an image may be installed is the
one in the MCUboot header of the file that actually arrives — that copy is
inside the region the signature covers. The version in `manifest.json` is a
hint, used to avoid a 240 kB download when there is nothing new. The app refuses
an image whose header disagrees with the manifest that offered it, and refuses
anything older than what the controller is already running.

The private key is not in any repository, and never has been.

## The manifest

```json
{
  "format": 1,
  "version": "0.1.0+2",
  "url": "https://github.com/MikaRau/rcontrol-releases/releases/download/v0.1.0.2/rcontrol-0.1.0.2.signed.bin",
  "size": 246067,
  "notes": "One line, shown in the app."
}
```

`format` is bumped only for a change that would make an already-installed app
read this file *wrongly* — never for adding a field, which older apps ignore. A
bump strands every phone until it is updated by hand.

`version` carries the build number after a `+`. Tags and filenames write it as a
`.` instead — `0.1.0+2` is released as `v0.1.0.2` — so that nothing between here
and a phone gets a chance to be clever about a reserved character.

## Installing one

From the app: **Settings → Firmware → Check**, then **Update firmware**. It needs
somebody at the controller, because the update door opens only for a minute
after its button is held for five seconds, and one press buys one update. The
car will not move while an update runs.
