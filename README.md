# Pixel 6 (raviole / gs101) Kernel CI

An automated CI/CD workflow leveraging GitHub Actions and Google's Kleaf (Bazel) build system to compile Android kernel distribution artifacts for Pixel 6 series devices (`raviole` / `gs101` platform).

## Features

- **Automated Weekly Builds**: Scheduled cron builds combined with manual trigger support (`workflow_dispatch`).
- **Disk Space Optimization**:
  - Maximizes runner disk space via `easimon/maximize-build-space` (~40GB+ free space).
  - Automatically purges `.repo` directory post-sync to reclaim ~39GB.
- **Kleaf / Bazel Optimizations**:
  - Memory-capped Bazel execution to prevent OOM errors on GitHub runners.
  - Enabled ThinLTO for optimized compilation speed.
  - Suppressed interactive progress bars for clean, manageable CI logs.
- **Automatic GitHub Releases**:
  - Generates release tags formatted as `YYYY.MM.DD-<commit_sha>`.
  - Extracts the short 8-character commit hash from the kernel source tree (`common`).
  - Bundles build artifacts (`out/slider/dist/*`) into an uncompressed ZIP (`zip -0`) attached directly to GitHub Releases.

## Workflow Overview

1. **Space Cleanup**: Removes unused runner pre-installed software (Android SDK, .NET, Docker images, CodeQL).
2. **Environment Setup**: Installs required build tools, `libbz2-dev`, and Google `repo`.
3. **Repo Sync**:
   - Clones kernel manifest (`gs-android17-6.18-gs101`).
   - Retrieves the 8-character commit hash from `common`.
   - Clears `.repo` to free ~39GB disk space.
4. **Kernel Compilation**: Executes `tools/bazel run` with target `//private/google-modules/soc/gs:slider_dist`.
5. **Artifact Packaging**: Stores distribution output cleanly.
6. **Release**: Publishes tag and attaches `raviole-kernel-slider.zip` to GitHub Release.

## Prerequisites

To run or fork this workflow:
- Ensure the workflow YAML resides in your repository's **default branch** (`main` or `master`) for scheduled cron jobs to activate.
- Ensure GitHub Actions workflow permissions are configured with **Read and write permissions** under `Settings` -> `Actions` -> `General` -> `Workflow permissions`.

## Manual Build Triggers (`workflow_dispatch`)

You can manually trigger a build from the GitHub Actions UI with custom parameters:

| Input | Description | Default |
| :--- | :--- | :--- |
| `MANIFEST_URL` | Android Kernel Manifest Repository URL | `https://android.googlesource.com/kernel/manifest` |
| `MANIFEST_BRANCH` | Manifest Branch | `gs-android17-6.18-gs101` |
| `PUBLISH_TYPE` | Github Publication Method  | `both` |
## License

- This project is licensed under the [Apache License 2.0](LICENSE). 
- Kernel sources compiled by this workflow are governed by their respective upstream licenses.
