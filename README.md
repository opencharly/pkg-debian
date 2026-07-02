# opencharly-debian — native DEB packaging for `charly` (Debian + Ubuntu)

`debian/` builds the `opencharly` `.deb` (the `charly` CLI at `/usr/bin/charly`) for
Debian and Ubuntu. ONE source serves both (Ubuntu inherits Debian's `deb`
format).

Consumed two ways, both through the SAME
`distro.debian.format.deb.local_pkg.build_template` in the embedded build
vocabulary (`charly/charly.yml`):

- **localpkg deploy** — `charly deploy` / `charly update` / `charly eval run` to a `target:
  vm` (or `target: local`) Debian/Ubuntu target builds this `.deb` on the host
  (in a debian container) and `apt-get install`s it onto the target,
  auto-resolving the mandatory deps.
- **release artifacts** — `task pkg:debian` builds a downloadable `.deb` into
  `dist/`.

The build_template copies a prebuilt `charly` binary into the source tree as `./charly`
(installed via `debian/opencharly.install`) and rewrites `debian/changelog`'s
version to the binary's CalVer, so `dpkg -s opencharly` matches `charly version` and
every build is upgradeable.

**Tailscale is intentionally NOT a `Depends:`** — it is not in Debian main. The
`charly` layer (`candy/charly/candy.yml`) supplies tailscale for Debian/Ubuntu via the
tailscale apt repo. All other mandatory deps are in Debian/Ubuntu main and
auto-resolve.

History lives in this repo's `CHANGELOG/` (one file per month).
