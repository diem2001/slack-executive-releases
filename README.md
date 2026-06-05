# Slack Executive — Releases

This repository is the **update-distribution channel** for the private Slack Executive desktop app.

It holds **only release artifacts** — there is **no application source code here**. The app itself is developed in a separate private repository; this public repo exists solely so installed clients can discover and download verified updates over GitHub's CDN, without exposing source or shipping an access token.

## What you'll find here

Each release (tagged `v<version>`) contains:

- `latest.json` — the [Tauri v2 updater manifest](https://v2.tauri.app/plugin/updater/) consumed by the in-app updater (version, notes, publish date, and the signed `darwin-aarch64` platform entry).
- `Slack Executive.app.tar.gz` — the notarized + stapled macOS app bundle, repacked from the stapled `.app`.
- `Slack Executive.app.tar.gz.sig` — the minisign signature for the tarball above.
- `*.dmg` — the standalone macOS installer.

## Verification

Update artifacts are signed with minisign; the in-app updater verifies the signature against the public key baked into the app before installing. The published `.app` is Apple Developer ID–signed, notarized, and stapled.

## Latest manifest

```
https://github.com/diem2001/slack-executive-releases/releases/latest/download/latest.json
```
