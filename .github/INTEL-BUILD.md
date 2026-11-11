# Intel macOS build in this fork

Open Actions → Build Intel macOS DMG → Run workflow. Keep `source_ref` at `dsh-v0.2.2-rc.2` for that release, or select another official tag or commit that supports the unsigned Intel packaging command. The workflow checks out the official repository at that ref; changes to product source in this fork are not included.

GitHub's `macos-15-intel` runner installs Node.js 24 and the source's pinned pnpm, installs locked dependencies, and executes `pnpm run package:desktop:mac:x64:unsigned`. The packaging command performs the build and runtime integrity and smoke checks. No signing secrets or self-hosted runners are required.

After success, download `deepseek-harness-mac-x64-<run-id>` from the run's Artifacts section. It contains the DMG and `SHA256SUMS.txt`. Artifacts and packaging diagnostics are retained for 14 days. The run summary records the actual product version and source commit.

The application uses a local ad-hoc signature and local-build branding; it has no Apple notarization, automatic update feed, or Computer Use helper. macOS may require permission in System Settings → Privacy & Security to open it. Test the app separately before replacing an existing installation.

Only manual dispatch starts this workflow. It does not publish a GitHub Release or upload to DeepSeek's download service. Public repositories can use standard GitHub-hosted runners without runner-minute charges under GitHub's current Actions policy; storage limits still apply, and private repositories are subject to the account's Actions allowance and billing settings.
