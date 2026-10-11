# ISSUE-006 — Build: independent Swift compilers run sequentially

State: PR-Ready
Authorized-Work: Pull-Request-Implementation
Publication-Target: New-pull-request
External-Reference: Not published.
Contribution-Priority: Medium
Root-Cause-Confidence: High
Finding-Category: Performance
Created: 2026-10-11
Updated: 2026-10-11
Source: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`

## Root-Cause

[S] `scripts/build.sh` completes extension compilation before starting the independent helper compiler.
This record corresponds to reviewed finding P1.

## Reach-and-Impact

[S] Every source build, including `install.sh`, executes both compilers.
[O] An isolated warm compiler comparison took 3.06 seconds sequentially and 2.14 seconds concurrently.
The comparison excludes packaging, signing, installation, and Finder interaction.

## Evidence

- [S] `scripts/build.sh:19-25` reads shared sources but writes separate executables.
- [S] `scripts/build.sh:29-30` signs the extension before its enclosing app.
- [O] Both compilation orders succeeded on macOS 15.7, arm64, Swift 6.1.2, with Swift language mode 5.

## Prior-Art

Coverage: upstream issue and PR searches for build, compile, parallel, swiftc, icon, render, PNG, and make-icon.
Releases v1.0.0 through v1.2.0 and the upstream root tree were inspected on 2026-10-11.
Discussions are disabled; no contribution guide or PR template exists in the current upstream tree.
Gaps: no exhaustive repository-wide issue-body search is claimed.
The build search returned localization issue #1, which does not own compiler scheduling.
No matching upstream PR was returned.
Contribution fit: one source-only PR changing compiler scheduling without installer or registration changes.

## Proposed-Change

Start exactly two compiler jobs, collect both successful results before signing, and preserve compiler failures.
Keep interruption cleanup bounded to the started jobs and retain extension-before-app signing.

## Scope-and-Constraints

- Preserve Bash 3.2, compiler flags, SDK selection, macOS 13 target, source inputs, bundle paths, and signing order.
- Exclude installer changes, new dependencies, new tests, generated scaffolding, and unrelated cleanup.
- User authorized scoped implementation, fork pushes, and immediate upstream PR submission.
- Parallel compilation may increase peak resource usage; no universal speedup is claimed.

## Verification

- [O] The native upstream and patched build scripts completed successfully in disposable output directories.
- [O] `codesign --verify --deep --strict` accepted the patched app and nested extension.
- [O] Real extension and helper type errors each returned 1, skipped bundle signing, and left no compiler running.
- [O] SIGTERM returned 143 and SIGINT returned 130; both owned compiler process groups disappeared.

## Performance-Evidence

[O] Earlier component trial: sequential=3.06 seconds; parallel=2.14 seconds; one warm comparison.
The first extension-only compiler trial took 35.60 seconds; its initialization cost was not profiled.
[O] Final full-build trial: upstream=3.25 seconds; patched=2.19 seconds; warm SDK; one comparison.
Packaging and signing were included; installation, cold builds, and Finder interaction were not measured.

## Publication-Blockers

None.

## Next-Action

Summary: Submit parallel build PR
Action: Submit the verified source-only contribution to nikgrbn/finder-menu main.
Done-When: The external PR URL is recorded and the ledger is validated and pushed.

## Pull-Request-Implementation

Branch: perf/issue-006-parallel-build
Base: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`
Scope: Parallelize the two existing compiler commands while preserving failure and signing boundaries.
Commit: ff52b55e0199ce3a5fbaa1605ff0fb7c75f88b23
Push: origin/perf/issue-006-parallel-build
Checks:

- `/bin/bash /tmp/finder-menu-pr-performance.KiwUBf/build/scripts/build.sh /tmp/finder-menu-pr-performance.KiwUBf/build-output` → native build succeeded; macOS 15.7, arm64, Bash 3.2.
- `codesign --verify --deep --strict "/tmp/finder-menu-pr-performance.KiwUBf/build-output/Finder Menu.app"` → valid app and nested extension signatures.
- Temporary native failure and signal checks → compiler failures=1, SIGTERM=143, SIGINT=130, surviving groups=0.
- `git diff --name-only upstream/main...HEAD` → only `scripts/build.sh`.

## Publication-Draft

Target: https://github.com/nikgrbn/finder-menu, base=main, head=MikeeI:perf/issue-006-parallel-build
Title: Compile helper and Finder extension concurrently

Body:

```markdown
## Summary

The helper and Finder extension compile from independent inputs into separate executables.
Run the two existing Swift compiler commands concurrently, then join both before signing.
Preserve extension-first error selection and extension-before-app signing.
Separate process groups and scoped cleanup also stop compiler descendants when the build is interrupted.
The script remains compatible with macOS Bash 3.2 and uses the existing compiler flags and SDK selection.

## Verification

- Built the complete app on macOS 15.7, arm64, with Swift 6.1.2 and Swift language mode 5.
- Verified the app and nested extension with `codesign --verify --deep --strict`.
- Injected real Swift type errors into each compiler input: exit 1, no bundle signing, no surviving compiler.
- Sent SIGTERM and SIGINT during compilation: exits 143 and 130, with both compiler process groups gone.
- No app installation or Finder extension registration was performed.

## Performance

One warm full-build comparison took 3.25 seconds upstream and 2.19 seconds with this patch.
These timings include packaging and signing, but not installation.
Concurrent compilation can increase peak resource usage; this is not a universal speedup claim.

### Disclosure

Investigated thoroughly with GPT-6.1 Sol (extra high reasoning effort), using [Oh My Pi](https://github.com/can1357/oh-my-pi) as the agent framework.
I reviewed this contribution with GPT-6.1 Sol at xhigh reasoning effort.

This report is not generic or unreviewed AI-generated output.
Its claims were checked against the cited evidence, and it includes the relevant detail intended to help maintainers resolve the issue.

If reports like this are not useful to the project, please let me know and I will refrain from submitting similar ones.
My intent is to help without wasting maintainer time or energy or discouraging their work.

Thank you for your work.
```
