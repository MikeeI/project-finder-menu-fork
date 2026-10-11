# ISSUE-002 — Installation: source aliases and copy failures remove the working app

State: Submitted
Authorized-Work: Pull-Request-Implementation
Publication-Target: New-pull-request
External-Reference: https://github.com/nikgrbn/finder-menu/pull/4
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
[O] An isolated native macOS reproduction returned exit 1 after deleting its own source bundle.

## Evidence

- [S] `scripts/finder-menu:16-23` accepts a directory, deletes the destination, and then copies the source.
- [S] `install.sh:7-12` builds separately and delegates installation to `enable`.
- [S] `scripts/finder-menu:7` exits on copying failure without restoring the old bundle.
- [O] Native Bash smoke passed source equality, source symlink, unreadable copy, injected promotion failure, and cleanup.
- The smoke used native filesystem tools while suppressing process termination and registration.

## Prior-Art

Coverage: all upstream issues, PRs, and release notes; checked=2026-10-11.
Gaps: None; upstream discussions are disabled.
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

- [O] `/bin/bash finder-menu-install-smoke.sh` passed on macOS 15.7 with disposable native filesystem paths.
- [O] Copy failure preserved the old bundle before the process-stop call; injected promotion failure restored it.
- Finder registration and live extension activation were intentionally outside this filesystem smoke.

## Publication-Blockers

None.

## Next-Action

Summary: Monitor upstream PR feedback
Action: Review maintainer feedback on https://github.com/nikgrbn/finder-menu/pull/4.
Done-When: Feedback is addressed or an evidence-backed merge or closure disposition is recorded.

## Pull-Request-Implementation

Branch: fix/issue-002-safe-install
Base: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`
Scope: Stage app replacement and preserve the prior bundle on exchange failure.
Commit: 7c86366bcd9188c3dbafa1973cac60688113d1b1
Push: origin/fix/issue-002-safe-install
Checks:

- Isolated native filesystem and lifecycle smoke: passed.

## Publication-Draft

Target: https://github.com/nikgrbn/finder-menu, base `main`, head `MikeeI:fix/issue-002-safe-install`.
Title: Stage app replacement before removing the installed bundle

Body:

```markdown
## Problem

`finder-menu enable [app]` removes the installed bundle before copying the replacement.
Supplying the installed app itself therefore deletes the source and fails the subsequent copy.
A copy error can also remove a previously working installation.
The current ordering is visible at https://github.com/nikgrbn/finder-menu/blob/e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3/scripts/finder-menu#L14-L23.

## Change

Prepare a separate copy under the destination directory before stopping the extension or moving the installed app.
Move the old bundle aside, promote the staged copy, and restore the old bundle if promotion fails.
An isolated subshell owns cleanup; a blocked restoration preserves and reports the backup instead of deleting it.
Follow a command-line source symlink while preserving symlinks inside the bundle.
Keep the existing registration and activation retry behavior.

This is ordinary-exit recovery, not an atomic directory exchange or a crash-safe transaction.
Failures after successful promotion retain the newly installed bundle, as before.

## Verification

- An isolated macOS 15.7 native Bash reproduction confirmed that the original source-equals-destination invocation deletes its source and exits 1.
- The corrected implementation passed source equality, a source symlink, an unreadable-file copy failure, and successful replacement with staging cleanup.
- An injected promotion failure restored the previous bundle using native filesystem moves.
- Process termination and extension registration were suppressed in this filesystem smoke; no live installation was replaced.

### Disclosure

Investigated thoroughly with GPT-6.1 Sol (extra high reasoning effort), using [Oh My Pi](https://github.com/can1357/oh-my-pi) as the agent framework.
I reviewed this contribution with GPT-6.1 Sol at xhigh reasoning effort.

This report is not generic or unreviewed AI-generated output.
Its claims were checked against the cited evidence, and it includes the relevant detail intended to help maintainers resolve the issue.

If reports like this are not useful to the project, please let me know and I will refrain from submitting similar ones.
My intent is to help without wasting maintainer time or energy or discouraging their work.

Thank you for your work.
```
