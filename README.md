# todo-dodo-release

The update channel for the [Todo-Dodo](https://github.com/dluxhu/todo-dodo)
desktop app. The app polls a channel manifest here, and downloads the signed
bundle the manifest names.

**This repository is generated.** `scripts/release-publish.sh` in the main repo
writes every file below. Do not edit it by hand — the next publish overwrites it.

## Layout

```
stable.json                              the manifest the shipped app polls
download/v1.2.3/Todo-Dodo.app.tar.gz     the bundle the updater installs
download/v1.2.3/Todo-Dodo.app.tar.gz.sig its minisign signature
download/v1.2.3/Todo-Dodo_1.2.3_universal.dmg   the download for a fresh install
```

One manifest per channel, at the repository root. `stable.json` is the only one
today; a `beta.json` would sit beside it, pointing at its own versioned
directory under `download/`. Adding a channel is a new manifest plus a build
configured to poll it — the directory layout does not change.

Versioned directories are never rewritten. A published version keeps its
artifacts forever, because an app that has not been opened in a year still asks
for the version its manifest named at the time.

## What the manifest says

```json
{
  "version": "1.2.3",
  "notes": "…",
  "pub_date": "2026-01-01T00:00:00Z",
  "platforms": {
    "darwin-aarch64": { "signature": "…", "url": "…/download/v1.2.3/Todo-Dodo.app.tar.gz" },
    "darwin-x86_64":  { "signature": "…", "url": "…/download/v1.2.3/Todo-Dodo.app.tar.gz" }
  }
}
```

Both architectures point at the same file: the bundle is a universal binary, and
an Intel Mac must not be handed an arm64-only one.

`signature` is the full contents of the `.sig`, not a path. The app verifies it
against a public key compiled into the binary, so a bundle this repository
serves cannot be swapped for another without the private key — which is why
this repository being public costs nothing.
