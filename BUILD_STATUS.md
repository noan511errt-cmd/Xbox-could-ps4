# Build status for this delivered source archive

- Source branding changed to **Nawaf Cloud Gaming**.
- PS4 package metadata defaults to title `Nawaf Cloud Gaming`, package version
  `01.56`, and stable Title ID `PSBX00001`.
- Added a GitHub Actions release workflow to compile and attach a PS4 GoldHEN
  `.pkg` and SHA-256 checksum when a matching GitHub Release is published.
- Replaced the previous source-check workflow that performed broad, unsafe
  `sed` renames with a small source-validation workflow.
- `bash -n` passed for the PS4 package and toolchain scripts.
- `tools/check-platform-boundaries.sh` passed.

A `.pkg` binary is **not included** in this archive. This build environment
has no OpenOrbis PS4 toolchain and the Git submodules are empty/uninitialized,
so the PS4 executable and package could not be compiled or tested here. The
release workflow performs that build in GitHub Actions after the source is
pushed with recursive submodules available. Actual installation, controller
pairing and firmware compatibility still require testing on the PS4 console.
