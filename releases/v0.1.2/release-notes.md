# Release 0.1.2

This release is prepared for online test distribution. It focuses on the
Android Maven, Apple SwiftPM, public distribution repository, and release
version-consistency workflows.

## Added

- Added Android Maven Central publishing scaffolding:
  - `maven-publish`
  - signing
  - POM metadata injection
  - GitHub Actions workflow
- Added Apple `SPM + XCFramework` release scaffolding:
  - single `ScannerSDK.xcframework.zip`
  - checksum generation
  - release-mode `Package.swift`
  - GitHub Actions workflow
- Added a unified release-version check script:
  - `tools/release/verify-release-version.sh`
- Added release documentation for Android Maven Central, Apple SwiftPM,
  cross-platform distribution, and the release checklist.

## Changed

- Changed the project license to `Netum Scanner SDK License`: the SDK is free
  for commercial application integration, closed-source, and must not be
  redistributed as a standalone SDK, component library, or source package.
- Updated Swift `CNSDK.h` to use stable package-local header references.
- Added explicit `.NET` package version metadata and release-version checking.
- Updated the Android demo version to `0.1.2` and `versionCode` to `2` for
  online test upload.

## Documentation

- Added SDK capability matrix, quickstart, platform permissions,
  troubleshooting, security and privacy, versioning and ABI, and documentation
  governance guides.
- Added Markdown documentation checks and Docs CI.
- Updated Android and Apple wrapper README files for public distribution.

## Release Status

- Android Maven Central: publishing scaffolding and dry-run validation path are
  ready. Central Portal, signing, and distribution-repository credentials must
  be injected before upload.
- Apple SwiftPM: XCFramework packaging, checksum generation, release-mode
  `Package.swift`, and distribution-repository export are ready.
- Consumer validation: after publication, validate the release from a clean
  Android app and a clean SwiftPM project.

## Validation

- Release executor must record the actual result for:
  - `./tools/release/verify-release-version.sh 0.1.2`
  - `python3 tools/dev/check_mobile_public_api_baseline.py`
  - Android release dry run
  - Apple release dry run
  - external consumer validation
