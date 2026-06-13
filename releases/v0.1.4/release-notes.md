# Release 0.1.4

This release ships the session initialization stage callback capability and
syncs demo support plus refreshed Apple distribution artifacts.

## Added

- Added a typed `initializeSession` stage callback across core, C API, Android
  Kotlin, Apple Swift, and the .NET wrapper.
- Added initialization-stage diagnostics and event observation in the Android,
  iOS, and WPF demos.

## Changed

- Updated the project version to `0.1.4`.
- Updated the Android demo version to `0.1.4` and `versionCode` to `4`.
- Updated the `.NET` wrapper package `Version` to `0.1.4`.
- Refreshed the Apple `xcframework` distribution artifacts so the new session
  initialization stage callback symbols and headers are included.

## Documentation

- Updated the README current release version.
- Updated Android Maven and Apple SwiftPM public integration examples.
- Added the `0.1.4` release note.

## Release Status

- Android Maven Central: pending GitHub Actions dry run, public distribution
  repository sync, Central Portal upload, and manual `Publish`.
- Apple SwiftPM: pending GitHub Actions dry run, public distribution repository
  sync, and private development repository `v0.1.4` GitHub Release.
- Consumer validation: after publication, validate the release from a clean
  Android app and a clean SwiftPM project.

## Validation

- Completed in this preparation round:
  - `./tools/release/verify-release-version.sh 0.1.4`
- Release executor must still record the actual result for:
  - `python3 tools/dev/check_mobile_public_api_baseline.py`
  - `./tools/release/build-android-local.sh`
  - Android release dry run
  - Apple release dry run
  - external consumer validation
