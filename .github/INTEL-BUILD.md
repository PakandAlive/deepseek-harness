# Intel macOS build in this fork

## Automatic official-release builds

The `Build Intel macOS DMG` workflow checks official GitHub Releases every 30 minutes (at minutes 17 and 47 of each UTC hour). It selects the most recently published non-draft `dsh-v*` release, including prereleases such as `rc` versions. It does not build unreleased master commits or attempt to backfill every historical release. GitHub scheduling delays and service outages can delay checks; this is polling, not an immediate upstream webhook.

The workflow resolves the official release tag to a commit SHA before packaging. A completed installer release in this fork named `intel-<official-tag>-ui-fix2`, containing a nonempty DMG and `SHA256SUMS.txt`, prevents duplicate builds. Failed builds and incomplete draft releases are retried on the next check while that version remains the latest release. A release becomes public only after both assets upload successfully.

Download installers from this fork's [Releases](https://github.com/PakandAlive/deepseek-harness/releases). The release notes link to the upstream source commit and build run. GitHub Release assets provide the durable download; run artifacts and packaging diagnostics are also retained for 14 days.

GitHub may disable scheduled workflows after 60 days without repository activity in a public repository. If that happens, re-enable this workflow in Actions. It runs from this fork's default branch, so keep the workflow changes when syncing upstream.

## Manual builds

Open Actions → Build Intel macOS DMG → Run workflow. Leave `source_ref` as `latest` to check the latest official release, or enter an official release tag such as `dsh-v0.2.2-rc.2`. Enable `force` to rebuild an installer already published in this fork. A forced rebuild replaces that release's matching assets. Arbitrary commits and unreleased tags are not accepted.

## Build behavior

GitHub's `macos-15-intel` runner installs Node.js 24 and the source's pinned pnpm, installs locked dependencies, and executes `pnpm run package:desktop:mac:x64:unsigned`. The packaging command performs the build and runtime integrity and smoke checks. No signing secrets or self-hosted runners are required. Only the packaging job receives repository-content write permission, for publishing installers to this fork; it does not upload to DeepSeek's download service.

Before dependency installation, the workflow checks out this fork's patch directory at the workflow commit and applies [unsigned desktop UI fixes](intel-patches/unsigned-ui.patch) to the official source. The `ui-fix2` release suffix distinguishes these installers from older unpatched releases. The sidebar uses the original DeepSeek whale and wordmark from the upstream artwork components, without the local-build label or version line; builds without a packaged update source stay idle instead of showing a retry error. This is still an unofficial fork build. Focused brand, sidebar, and updater tests run before packaging; the built-bundle smoke verifies the original wordmark is present and the local-build label is absent. If a future official version changes the patched code, patch application fails before packaging rather than silently publishing an unpatched installer.

The application uses a local ad-hoc signature; it has no Apple notarization, automatic application update feed, or Computer Use helper. macOS may require permission in System Settings → Privacy & Security to open it. Test the app separately before replacing an existing installation. Automating release builds does not add automatic updates to installed applications.

Public repositories can use standard GitHub-hosted runners without runner-minute charges under GitHub's current Actions policy; storage limits still apply, and private repositories are subject to the account's Actions allowance and billing settings.
