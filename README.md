# Intent Update Artifacts

This repository hosts Intent desktop application update artifacts for beta and stable release channels.

The repository is consumed by electron-updater via the generic provider:
- Base URL: https://github.com/intent-hq/cloudlands-releases/releases/download
- Channels: beta, stable

Each channel tag (beta, stable) is maintained as a rolling release pointing to the latest version for that channel.
Immutable versioned releases are tagged as v<version> (e.g., v1.0.0).

## Artifacts

For each release:
- macOS: .dmg (signed + notarized, arm64)
- Windows: .exe installer (unsigned)
- Linux: .AppImage and .deb (unsigned)

## Workflow

Releases are published by GitHub Actions in [intent-hq/cloudlands-fe](https://github.com/intent-hq/cloudlands-fe).
