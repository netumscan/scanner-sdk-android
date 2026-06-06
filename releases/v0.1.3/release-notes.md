# Release 0.1.3

This release prepares Scanner SDK `0.1.3` for publication. It focuses on
version alignment, public integration documentation, and release notes.

## Changed

- Updated the project version to `0.1.3`.
- Updated the Android demo version to `0.1.3` and `versionCode` to `3`.
- Updated the `.NET` wrapper package `Version` to `0.1.3`.

## Documentation

- Updated the README current release version.
- Updated Android Maven and Apple SwiftPM public integration examples.
- Added the `0.1.3` release note.

## Release Status

- Android Maven Central: pending GitHub Actions dry run, public distribution
  repository sync, Central Portal upload, and manual `Publish`.
- Apple SwiftPM: pending GitHub Actions dry run, public distribution repository
  sync, and private development repository `v0.1.3` GitHub Release.
- Consumer validation: after publication, validate the release from a clean
  Android app and a clean SwiftPM project.

## Validation

- Release executor must record the actual result for:
  - `./tools/release/verify-release-version.sh 0.1.3`
  - `python3 tools/dev/check_mobile_public_api_baseline.py`
  - `./tools/release/build-android-local.sh`
  - Android release dry run
  - Apple release dry run
  - external consumer validation
