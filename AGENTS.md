# AGENTS.md — opencharly/pkg-fedora

Native RPM packaging for the `charly` CLI (Fedora / rpm-family). The repo owns
`opencharly.spec`, which builds the `opencharly` RPM (the `charly` CLI at
`/usr/bin/charly`).

Canonical files:

- `opencharly.spec` — the RPM spec: `Requires:` for the mandatory runtime deps
  (all in the Fedora repos, including tailscale), `Suggests:` for the
  situational tooling (docker / GPU / k8s). The binary is passed as `%{ovbin}`
  and the version as `%{ovver}` (the binary's own CalVer).
- `CHANGELOG/` — history (one file per CalVer release).
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-internals:repo-setup` — the org landing automation (required workflow,
  native auto-merge, tag-on-merge CalVer) and the new-repo checklist.
- `/charly-tools:charly` — the `charly` toolchain candy and the runtime OS
  dependencies the package must carry.

## Build / validate / test

- `task pkg:fedora` — builds a downloadable `.rpm` into `dist/` (the release
  artifact path).
- The **localpkg deploy** path builds the RPM on the host (in a fedora container)
  and `dnf install`s it onto a Fedora `target: vm` / `target: local` — both paths
  share the SAME `distro.fedora.format.rpm.local_pkg.build_template` in charly's
  embedded vocabulary.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo carries no
  per-repo candy gate.

## Modify this repo

- Every mandatory runtime OS dependency belongs in the spec's `Requires:` (all
  must be present in the Fedora repos so `dnf install` auto-resolves); the
  situational tools are `Suggests:`.
- The binary + welded plugins are prebuilt on the host and bind-mounted into the
  build container; the spec only packages them.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
