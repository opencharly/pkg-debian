# AGENTS.md — opencharly/pkg-debian

Native DEB packaging for the `charly` CLI (Debian + Ubuntu; ONE source serves
both). The repo owns the `debian/` packaging tree that builds the `opencharly`
`.deb` (the `charly` CLI at `/usr/bin/charly`).

Canonical files:

- `debian/control` — the package metadata and the mandatory `Depends:`
  (tailscale is intentionally NOT a `Depends:` — it is not in Debian main; the
  charly candy supplies it).
- `debian/rules` — the debhelper rules; the binary + welded plugins are prebuilt
  and installed via `debian/opencharly.install`.
- `debian/changelog` — the version, rewritten to the binary's CalVer at build
  time so `dpkg -s opencharly` matches `charly version`.
- `debian/opencharly.install` — the install manifest (`charly` → `/usr/bin`,
  plugins → `/usr/lib/charly/plugins`).
- `CHANGELOG/` — history (one file per CalVer release).
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-internals:repo-setup` — the org landing automation (required workflow,
  native auto-merge, tag-on-merge CalVer) and the new-repo checklist.
- `/charly-tools:charly` — the `charly` toolchain candy and the runtime OS
  dependencies the package must carry.

## Build / validate / test

- `task pkg:debian` — builds a downloadable `.deb` into `dist/` (the release
  artifact path).
- The **localpkg deploy** path builds the `.deb` on the host (in a debian
  container) and `apt-get install`s it onto a Debian/Ubuntu `target: vm` /
  `target: local` — both paths share the SAME
  `distro.debian.format.deb.local_pkg.build_template` in charly's embedded
  vocabulary.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo carries no
  per-repo candy gate.

## Modify this repo

- Every mandatory runtime OS dependency belongs in `debian/control`'s
  `Depends:` (all must be in Debian/Ubuntu main so they auto-resolve); the
  situational tools (docker, nvidia, kubectl, cloudflared, gvisor-tap-vsock) are
  `Suggests:`. Tailscale stays out of `Depends:` by design.
- The binary + plugins are prebuilt and copied into the source tree by the shared
  build_template; there is no compile step in `debian/rules`.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
