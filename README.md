# todo-dodo-release

The public distribution point for Todo-Dodo: the **web app**, and the **update
channel** the desktop app polls.

Nothing here links to the app's source repository, which is private. This
repository, its releases and the manifests are all public, so a link would
disclose it.

## The web app

Runs entirely in the browser — a single self-contained HTML file, no server, no
account. Data stays in the browser's storage on the machine that loaded it.

| Channel | URL | What it is |
|---|---|---|
| **Stable** | https://dluxhu.github.io/todo-dodo-release/app/ | the latest tagged release |
| **Alpha** | https://dluxhu.github.io/todo-dodo-release/app/alpha/ | the current development build — expect breakage |

The two are separate origins' worth of storage in name only: they share
`dluxhu.github.io`, so **they share browser storage**. Opening alpha and stable
in the same browser means one set of tasks, written by two different versions of
the app. Use a separate browser profile for alpha if that matters.

Folder sync needs the File System Access API, which exists only in desktop
Chromium browsers (Chrome, Edge, or Brave with the flag enabled). Everything
else works anywhere.

## Layout

```
app/index.html         the stable web app
app/alpha/index.html   the development build
stable.json            the manifest the shipped desktop app polls
.nojekyll              serve paths verbatim; skip the Jekyll build
```

**Only `stable.json` is generated** — the desktop release script writes it, and
the next publish overwrites it, so do not hand-edit that file. The README and
the `app/` builds are placed deliberately; editing them is fine.

Desktop binaries are **not** in git. Each lives as an asset on a GitHub release
here, because a submodule that accumulated every release would be re-downloaded
in full by anyone cloning with `--recurse-submodules`, forever.

Per release, as assets on the tag `vX.Y.Z`:

```
Todo-Dodo.app.tar.gz            the bundle the updater downloads and unpacks
Todo-Dodo.app.tar.gz.sig        its minisign signature
Todo-Dodo_X.Y.Z_universal.dmg   the download for a fresh install
```

GitHub attaches its own *Source code* archives to every release and there is no
way to switch them off. They are inert here: this repository contains a README,
a manifest and two built HTML files, so there is no source in them.

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
serves cannot be swapped for another without the private key — which is why this
repository being public costs nothing.
