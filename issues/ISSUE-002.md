# ISSUE-002 — Installation: source aliases and copy failures remove the working app

State: Implementing
Authorized-Work: Pull-Request-Implementation
Publication-Target: New-pull-request
External-Reference: Not published.
Contribution-Priority: High
Root-Cause-Confidence: High
Finding-Category: Reliability
Created: 2026-10-11
Updated: 2026-10-11
Source: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`

## Root-Cause

[S] `enable` deletes the installed bundle before preparing an independent replacement.
This record corresponds to review finding E2.

## Reach-and-Impact

[S] Explicitly supplying the installed bundle deletes the source before `cp` reads it.
[S] A failed replacement copy can leave the prior installation unavailable.
Native isolated reproduction is pending.

## Evidence

- [S] `scripts/finder-menu:16-23` accepts a directory, deletes the destination, and then copies the source.
- [S] `install.sh:7-12` builds separately and delegates installation to `enable`.
- [S] `scripts/finder-menu:7` exits on copying failure without restoring the old bundle.

## Prior-Art

Coverage: all upstream issues and PRs, including closed states; checked=2026-10-11.
Gaps: release-note bodies and discussion availability remain to be checked.
Issues #1 and #2 concern localization and SMB menu availability, not bundle replacement.
No upstream PR existed at intake.
Contribution fit: a scoped installation-ordering correction.

## Proposed-Change

Prepare a staged copy before stopping the extension or replacing the installed bundle.
Retain the previous bundle through the exchange and restore it if the exchange fails.

## Scope-and-Constraints

- Preserve Bash 3.2 support, activation retries, existing command grammar, and diagnostics.
- Do not claim crash-safe transactions or serialize unrelated installer invocations.
- Exclude new tests, registry redesign, and unrelated CLI cleanup.
- User authorized implementation and direct PR submission for the reviewed findings.

## Verification

- Use disposable native macOS paths to reproduce source-equals-destination deletion.
- Verify source alias, copy failure, exchange failure, and successful replacement without touching the real installation.

## Publication-Blockers

Native reproduction, bounded cleanup design, exact PR draft, and disclosure runtime evidence are pending.

## Next-Action

Summary: Implement staged app replacement
Action: Implement and verify the scoped replacement lifecycle with isolated native filesystem checks.
Done-When: The old bundle survives preparation or exchange failure and the contribution is ready for submission.

## Pull-Request-Implementation

Branch: fix/issue-002-safe-install
Base: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`
Scope: Stage app replacement and preserve the prior bundle on exchange failure.
Commit: Pending.
Push: Pending.
Checks:

- Pending native macOS verification.
