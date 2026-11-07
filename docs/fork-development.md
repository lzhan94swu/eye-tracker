# Building the independent Eye Tracker LZ fork

This fork has its own **macOS app name**, **bundle identifier**, **configuration/calibration directory**, **single-instance IPC**, **launch-at-login identifier**, and **release URL**. It does not overwrite the upstream `Eye Tracker.app`.

| Property | Fork value |
| --- | --- |
| Repository | `lzhan94swu/eye-tracker` |
| App | `Eye Tracker LZ.app` |
| Bundle ID | `io.github.lzhan94swu.eyetracker` |
| Runtime slug | `eye-tracker-lz` |
| Source command | `eye-tracker-lz` |
| Original app | `Eye Tracker.app`, unchanged |
| Fork configuration | `~/Library/Application Support/eye-tracker-lz/` |

## Run from source (macOS)

Requires macOS 14+, Apple silicon, Xcode Command Line Tools and `uv`.

```sh
git clone https://github.com/lzhan94swu/eye-tracker.git
cd eye-tracker
uv sync --locked
uv run eye-tracker-lz
```

The fork already uses a different default configuration directory and instance lock; `--config-dir` is optional. For experiments with a *third* isolated profile:

```sh
uv run eye-tracker-lz --config-dir "$HOME/eye-tracker-lz-test"
```

## Download a pre-release test build (no version tag needed)

Open the repository's **Actions → macOS Preview** workflow. On pull requests touching app/build code, or when manually dispatched through **Run workflow**, it builds the app on an Apple silicon macOS runner, signs it ad hoc, verifies the bundle ID and runs a CLI smoke test. Download the **EyeTracker-LZ-macos-arm64-preview** artifact; unzip it to obtain the DMG.

If the workflow is disabled on the new fork, enable GitHub Actions in the repository UI. The GitHub connector cannot enable workflows or dispatch new runs from this chat.

The preview artifact is **not** a GitHub Release, and it is **not notarized**. macOS Gatekeeper may require you to open it explicitly. Ad-hoc signing gives the bundle a build-specific identity, so Accessibility and Camera permissions may need to be granted again after each rebuild. For stable permissions across updates, provide the repository with a reusable signing certificate using `MACOS_SIGNING_CERT_P12`, `MACOS_SIGNING_CERT_PASSWORD`, and optionally `MACOS_SIGNING_IDENTITY` for the full Release workflow.

## Publish a tagged release

The existing **Release** workflow supports a manual dry run (uploading Windows/macOS/Linux packages as Actions artifacts) and publishing on `vX.Y.Z` tag pushes. For a public release:

1. Update `__version__` in `src/eye_tracker/__init__.py` and move the corresponding `CHANGELOG.md` entry out of `[Unreleased]`.
2. Push a matching tag `vX.Y.Z` to **this fork**, not the upstream repository.
3. Wait for the Release workflow to finish; it publishes on this repository's Releases page.

CI does not publish a Release merely because a branch, fork, or pull request exists. To test packaging now, use **macOS Preview** and leave `main` untouched until the PR is reviewed.

The package still retains the upstream MIT copyright and origin attribution. The fork's Linux and Windows packaging continue to use some original executable/file names internally for compatibility, but the installation and data identities are separate.
