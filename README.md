# Intent Update Artifacts

This repository hosts Intent desktop application update artifacts for beta and stable release channels.

The repository is consumed by electron-updater via the generic provider:
- Base URL: https://github.com/intent-hq/cloudlands-releases/releases/download
- Channels: beta, stable

Each channel tag (beta, stable) is maintained as a rolling release pointing to the latest version for that channel.
Immutable versioned releases are tagged as v&lt;version&gt; (e.g., v2.0.5).

## Artifacts

Each release currently publishes macOS arm64 artifacts only:

- **Intent-{version}-arm64.dmg** — signed and notarized disk image
- **Intent-{version}-arm64-mac.zip** — signed and notarized app bundle (ZIP)
- **Intent-{version}-arm64.dmg.blockmap** — delta update support for DMG
- **Intent-{version}-arm64-mac.zip.blockmap** — delta update support for ZIP
- **latest-mac.yml** — electron-updater feed manifest (version, URLs, sha512 checksums)

## Channels

### Beta (rolling tag: `beta`)

The `beta` release tag is updated **automatically** by the [Release Beta workflow](https://github.com/intent-hq/cloudlands-fe/blob/main/.github/workflows/release-beta.yml) in intent-hq/cloudlands-fe on every dispatch.

When the workflow runs:
1. Builds the app with the intentd sidecar
2. Signs and notarizes for macOS
3. Creates a versioned release (e.g., v2.0.5) with all assets
4. Updates the rolling `beta` release by replacing its assets with the new build

Beta channel users receive updates immediately after each release.

### Stable (rolling tag: `stable`)

The `stable` release tag is updated **manually** by promoting a versioned beta release. Stable is the default update channel for production users.

## Promoting to Stable

To promote a versioned release (e.g., v2.0.5) to the stable channel:

1. **Verify the beta release is correct:**
   ```bash
   gh release view v2.0.5 -R intent-hq/cloudlands-releases
   ```

2. **Delete old stable assets:**
   ```bash
   # List current assets to confirm what to delete
   gh release view stable -R intent-hq/cloudlands-releases
   
   # Delete each asset (adjust filenames for the old version)
   gh release delete-asset stable Intent-{old-version}-arm64.dmg -R intent-hq/cloudlands-releases -y
   gh release delete-asset stable Intent-{old-version}-arm64.dmg.blockmap -R intent-hq/cloudlands-releases -y
   gh release delete-asset stable Intent-{old-version}-arm64-mac.zip -R intent-hq/cloudlands-releases -y
   gh release delete-asset stable Intent-{old-version}-arm64-mac.zip.blockmap -R intent-hq/cloudlands-releases -y
   gh release delete-asset stable latest-mac.yml -R intent-hq/cloudlands-releases -y
   ```

3. **Upload new assets from the versioned release:**
   ```bash
   # Download assets from the versioned release
   gh release download v2.0.5 -R intent-hq/cloudlands-releases -D /tmp/v2.0.5
   
   # Upload all assets to stable
   gh release upload stable /tmp/v2.0.5/* -R intent-hq/cloudlands-releases --clobber
   ```

4. **Verify the stable feed points to the new version:**
   ```bash
   curl -sL https://github.com/intent-hq/cloudlands-releases/releases/download/stable/latest-mac.yml | grep version:
   ```

The beta channel and the versioned release (v2.0.5) remain untouched. Stable channel users will receive the promoted version on their next update check.

## Workflow

Releases are published by GitHub Actions in [intent-hq/cloudlands-fe](https://github.com/intent-hq/cloudlands-fe). See the [Release Beta workflow](https://github.com/intent-hq/cloudlands-fe/blob/main/.github/workflows/release-beta.yml) for implementation details.
