# Nawaf Cloud Gaming — PS4 build and release notes

This repository is a branded customization of the upstream PSBox Cloud Gaming
codebase. The original GPL-3.0-or-later license and third-party notices remain
included. Keep those notices when publishing or distributing modified builds.

## PS4 identity

- Home-screen name: **Nawaf Cloud Gaming**
- Package filename: `Nawaf Cloud Gaming - PS4.pkg`
- GoldHEN target: PS4
- Title ID: `PSBX00001` (kept stable so later packages can update the same app)
- Initial customized package version: `01.56`
- CMake source version: `1.6.4`

Do not change the Title ID or CONTENT_ID after installing the first customized
PKG if you want subsequent packages to be recognized as updates to the same
application. A package still needs to be built with the OpenOrbis toolchain and
installed through GoldHEN; this repository does not include Sony signing keys.

## Publish a new PS4 package

1. Commit your changes to your own GitHub repository.
2. Increase `project(... VERSION ...)` in `CMakeLists.txt` for each release.
3. Update `XC_PS4_VERSION` in `.github/workflows/release-ps4.yml` to the next
   PS4 package version (for example `01.57`). Keep it within the SFO version
   format accepted by the packaging script.
4. Create and publish a matching GitHub Release tag, such as `v1.6.5`.
5. The `Release (PS4 GoldHEN PKG)` workflow builds the optimized PS4 binary,
   packages it, and attaches the `.pkg` plus a SHA-256 checksum to that release.
6. Download the new PKG from your repository's Releases page and install it
   with GoldHEN Package Installer. The PS4 edition currently uses manual PKG
   installation; it does not silently download or install updates itself.

The release workflow is build automation, not proof that the app works on every
PS4 firmware or controller model. Validate each package on the target console.

## Build locally

```bash
git clone --recursive YOUR-REPOSITORY-URL
cd YOUR-REPOSITORY
sudo apt-get install build-essential cmake ninja-build clang-18 lld-18 llvm-18 \
  pkg-config git curl wget python3 nasm yasm zlib1g-dev libssl-dev libxml2-dev
bash tools/ps4/fetch-toolchain.sh ./ps4-toolchain 18
cmake -S . -B build-ps4 -G Ninja \
  -DCMAKE_TOOLCHAIN_FILE=cmake/ps4.toolchain.cmake \
  -DCMAKE_BUILD_TYPE=Release
cmake --build build-ps4 --parallel 2
XC_PS4_TITLE='Nawaf Cloud Gaming' \
XC_PS4_VERSION='01.56' \
XC_PS4_TITLE_ID='PSBX00001' \
XC_PS4_CONTENT_ID='IV0000-PSBX00001_00-PSBOXCLOUDGAME00' \
  bash tools/ps4/package.sh build-ps4 build-ps4/pkg
```

## Controller troubleshooting

The current PS4 input backend reads the console's native `scePad` state and
maps DualShock 4 buttons, sticks, triggers, touchpad, gyro and acceleration.
This is the existing backend, not a newly verified hardware fix. If a pad is
not detected, test it while signed into a local PS4 user, reconnect it by USB,
then inspect the application log for `scePadOpen` / `scePadReadState` errors.
Do not claim compatibility with third-party or Bluetooth-only controllers
without testing them on the console.

## Custom release feed

The original in-app release-check implementation still points to the upstream
project's release API. The PS4 build does not self-update, so install your own
repository's PKG manually from its Releases page. To point any future updater
work at your fork, first set the exact GitHub owner/repository in
`src/app/update_check.cpp` and review the signed-update trust model in
`src/app/updater.cpp`; do not simply remove signature verification.
