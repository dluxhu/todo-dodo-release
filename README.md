# todo-dodo-release

The update channel for the Todo-Dodo desktop app. The app polls a channel
manifest here, and downloads the signed bundle the manifest names.

Nothing here links to the app's source repository, which is private. This
repository, its releases and the manifests are all public, so a link would
disclose it.

**This repository is generated.** The app's release script writes every file
below. Do not edit it by hand — the next publish overwrites it.

## Layout

The repository holds **only the channel manifests**. Every binary lives on a
GitHub release here, not in git — a submodule that accumulated each release
would be re-downloaded in full by anyone cloning the main repo with
`--recurse-submodules`, forever.

```
stable.json          the manifest the shipped app polls
```

Per release, as assets on the tag `vX.Y.Z`:

```
Todo-Dodo.app.tar.gz       the bundle the updater downloads and unpacks
Todo-Dodo.app.tar.gz.sig   its minisign signature
Todo-Dodo_X.Y.Z_universal.dmg   the download for a fresh install
```

GitHub attaches its own *Source code* archives to every release and there is no
way to switch them off. They are inert here: this repository contains a README
and a manifest, so there is no source in them.

One manifest per channel, at the repository root. `stable.json` is the only one
today; a `beta.json` would sit beside it, pointing at its own releases. Adding a
channel is a new manifest plus a build configured to poll it.

A published release is never rewritten or deleted. An app that has not been
opened in a year still asks for exactly what the manifest named at the time.

## What the manifest says

```json
{
  "version": "1.2.3",
  "notes": "…",
  "pub_date": "2026-01-01T00:00:00Z",
  "platforms": {
    "darwin-aarch64": { "signature": "…", "url": "…/releases/download/v1.2.3/Todo-Dodo.app.tar.gz" },
    "darwin-x86_64":  { "signature": "…", "url": "…/releases/download/v1.2.3/Todo-Dodo.app.tar.gz" }
  }
}
```

Both architectures point at the same file: the bundle is a universal binary, and
an Intel Mac must not be handed an arm64-only one.

`signature` is the full contents of the `.sig`, not a path. The app verifies it
against a public key compiled into the binary, so a bundle this repository
serves cannot be swapped for another without the private key — which is why
this repository being public costs nothing.
