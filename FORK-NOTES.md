# Fork notes - todor-wdb/adb-ap

Our fork of [AdbAutoPlayer/AdbAutoPlayer](https://github.com/AdbAutoPlayer/AdbAutoPlayer) (MIT).
Created 2026-08-25. This file exists only on our branches; `main` is kept as a clean mirror of
upstream.

## The policy: one-way, downstream only

**We merge upstream in. We never open pull requests upstream.** Our changes are ours; the fork is
a standalone downstream copy, not a contribution path.

| We do | We do not |
|---|---|
| `git fetch upstream && git merge upstream/main` into our branch | Open a PR against `AdbAutoPlayer/AdbAutoPlayer` |
| Keep `main` untouched, tracking upstream | Commit our work to `main` |
| Keep local work on `webdevbar` (or a topic branch off it) | Push branches upstream |

Keeping `main` pristine is what makes "how far behind are we" answerable at a glance and keeps
merges trivial.

## Why this repo exists, and what was wrong with the previous one

`WebDevBar/AdbAutoPlayer` is **not a GitHub fork**: the API reports `fork: false` with
`parent: null`, because it was created as a standalone repo and pushed rather than made with the
Fork button. Git-wise nothing is broken - the history is shared and `git remote add upstream` works
fine - but GitHub does not place it in upstream's network, so:

- no **Sync fork** button;
- no cross-repo compare - `gh api repos/.../compare/AdbAutoPlayer:main...` returns 404;
- no way to see how far behind upstream it has drifted without doing it by hand locally.

`todor-wdb/adb-ap` is a real fork (`fork: true`, parent `AdbAutoPlayer/AdbAutoPlayer`), so all of
that works.

The name is `adb-ap` rather than `adbautoplayer` because `todor-wdb/adbautoplayer` is not free: it
resolves by redirect to `WebDevBar/AdbAutoPlayer`, which was transferred out of the personal account.

**The old copy is still live and untouched.** Its known deltas are a Fedora RPM release bump plus
two claims-audit comments, and an undecided `device_stream.py` revert parked on
`wip/device-stream-revert-2026-08-21`. The plan is to merge selectively from it later, then archive
it once nothing there is still needed. Nothing has been archived or deleted yet.

## Syncing with upstream

```bash
git remote -v                      # origin = todor-wdb/adb-ap, upstream = AdbAutoPlayer/AdbAutoPlayer
git fetch upstream
git checkout main && git merge --ff-only upstream/main && git push origin main
git checkout webdevbar && git merge main
```

## gh gotcha

`gh` resolves a fork to its **parent** by default, so every `gh` command - reads included - would
target upstream, and the errors do not say so (a `gh release view` would report "not found" for a
release that exists here). Run this once per clone:

```bash
gh repo set-default todor-wdb/adb-ap
gh repo set-default --view
```

## Where this is used

`drey-server`, headless: the Python bot core only (`src-tauri/src-python/adb_auto_player`), driven
over ADB against a headless Android AVD. No Tauri GUI, no RPM packaging. Setup and decisions live in
the [the-drey-setup](https://github.com/todor-wdb/the-drey-setup) repo under `machines/server/`.
