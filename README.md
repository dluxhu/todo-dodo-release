# todo-dodo-release

The public distribution point for Todo-Dodo: the **web app**, and the **update
channel** the desktop app polls.

Nothing here links to the app's source repository, which is private. This
repository, its releases and the manifests are all public, so a link would
disclose it.

## The web app

**https://dluxhu.github.io/todo-dodo-release/app/**

A single self-contained HTML file — no server, no account. Data stays in the
browser's storage on the machine that loaded it.

This is the **current development build**, not a tagged release. Expect
breakage, and keep an export if the data matters.

**One build, deliberately.** There is no second channel here, and adding one at
a path (`app/beta/`, say) would not work: browser storage is scoped by ORIGIN —
scheme, host and port — never by path. Two builds under `dluxhu.github.io` share
one storage area, so the newer one migrates the older one's database and then
deletes the source, which is correct on a real upgrade and destroys the other
channel's data here. A second channel needs a separate origin, not a
subdirectory.

Folder sync needs the File System Access API, which exists only in desktop
Chromium browsers (Chrome, Edge, or Brave with the flag enabled). Everything
else works anywhere.

## Layout

```
app/index.html   the web app
stable.json      the manifest the shipped desktop app polls
.nojekyll        serve paths verbatim; skip the Jekyll build
```

**Only `stable.json` is generated** — the desktop release script writes it, and
the next publish overwrites it, so do not hand-edit that file. The README and
the `app/` build are placed deliberately; editing them is fine.

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
a manifest and one built HTML file, so there is no source in them.

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
