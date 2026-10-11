# ISSUE-005 — Terminal reset: operational deletion errors are reported as success

State: PR-Ready
Authorized-Work: Pull-Request-Implementation
Publication-Target: New-pull-request
External-Reference: Not published.
Contribution-Priority: Low
Root-Cause-Confidence: High
Finding-Category: Reliability
Created: 2026-10-11
Updated: 2026-10-11
Source: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`

## Root-Cause

[S] `terminal auto` suppresses every `defaults delete` failure and prints success without establishing the requested state.
This record corresponds to review finding E5.

## Reach-and-Impact

[S] If an operational delete failure leaves a value, subsequent terminal actions still prioritize that value.
A missing key is an intentional idempotent-success case, not a defect.
[O] Native CLI smoke reproduced false success under a failed preference subprocess and verified corrected failure propagation.
The OS-level synchronization failure itself was not induced.

## Evidence

- [S] `scripts/finder-menu:65-67` ignores deletion failure and prints the automatic-selection message.
- [S] `Sources/Shared/Settings.swift:25-28` uses an absent value for automatic selection.
- [S] `Sources/FinderMenu/main.swift:150` prioritizes a persisted selection.
- [O] Native JXA/CoreFoundation removal passed absent-domain, absent-key, and present-key cases in a disposable suite.
- [O] A failing subprocess retained the selection: the old CLI reported success, while the corrected CLI exited 1 silently.
- [S] Synchronization reports success or failure: https://developer.apple.com/documentation/corefoundation/cfpreferencesappsynchronize(_:).

## Prior-Art

Coverage: all upstream issues, PRs, and release notes; checked=2026-10-11.
Gaps: None; upstream discussions are disabled.
Issues #1 and #2 concern localization and SMB menu availability, not terminal reset errors.
No upstream PR existed at intake.
Contribution fit: a scoped native preference-removal and CLI error-contract correction.

## Proposed-Change

Use idempotent `CFPreferencesSetAppValue` removal and require successful `CFPreferencesAppSynchronize` before reporting success.

## Scope-and-Constraints

- Preserve command grammar, preferences ownership, and all other terminal selection paths.
- Do not infer absence from any failed preference read.
- Exclude new dependencies, parser machinery, and new tests.
- User authorized implementation and direct PR submission for the reviewed findings.

## Verification

- [O] Native CLI smoke passed absent-domain, absent-key, present-key removal, and unrelated-key preservation.
- [O] An injected failing subprocess returned exit 1 without a success message and retained the previous selection.
- The disposable CLI fixture changed only the domain literal; the user's real preference domain was untouched.
- Actual OS synchronization failure was not induced; its failure result is checked through the documented API contract.

## Publication-Blockers

None.

## Next-Action

Summary: Submit scoped upstream PR
Action: Submit the exact recorded draft to `nikgrbn/finder-menu` under the user's direct publication authorization.
Done-When: The upstream PR URL is recorded in this file and the index.

## Pull-Request-Implementation

Branch: fix/issue-005-terminal-reset
Base: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`
Scope: Correct the terminal-auto persistence error contract.
Commit: b174bb5c1e76345d3c270d8cfe5ca465a9bb16b3
Push: origin/fix/issue-005-terminal-reset
Checks:

- Native JXA/CoreFoundation reset and CLI subprocess-failure smoke: passed.

## Publication-Draft

Target: https://github.com/nikgrbn/finder-menu, base `main`, head `MikeeI:fix/issue-005-terminal-reset`.
Title: Report terminal reset failures without breaking absent-key resets

Body:

```markdown
## Problem

`finder-menu terminal auto` suppresses every `defaults delete` error and unconditionally reports automatic selection.
An operational error can therefore leave the saved selection in place while the command exits successfully.
A missing key is different: resetting an already automatic selection should remain successful.
The current branch is visible at https://github.com/nikgrbn/finder-menu/blob/e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3/scripts/finder-menu#L65-L67.

## Change

Use native CoreFoundation preference removal through the already available `osascript` tool.
Remove the key idempotently and check `CFPreferencesAppSynchronize` before reaching the success message.
This avoids parsing diagnostics or treating every failed read as proof of absence.
The synchronization result contract is documented at https://developer.apple.com/documentation/corefoundation/cfpreferencesappsynchronize(_:).

## Verification

- On macOS 15.7, the source-derived CLI passed absent-domain, absent-key, and present-key reset cases in a disposable preference suite.
- An unrelated preference remained intact.
- With a failing preference subprocess, the old CLI printed success and retained the selection.
- The corrected CLI exited 1 without printing success and retained the selection.
- The fixture changed only the preference-domain literal; no real Finder Menu preferences were altered.
- An OS-level synchronization failure was not induced; the documented failure result is explicitly checked.

### Disclosure

Investigated thoroughly with GPT-6.1 Sol (extra high reasoning effort), using [Oh My Pi](https://github.com/can1357/oh-my-pi) as the agent framework.
I reviewed this contribution with GPT-6.1 Sol at xhigh reasoning effort.

This report is not generic or unreviewed AI-generated output.
Its claims were checked against the cited evidence, and it includes the relevant detail intended to help maintainers resolve the issue.

If reports like this are not useful to the project, please let me know and I will refrain from submitting similar ones.
My intent is to help without wasting maintainer time or energy or discouraging their work.

Thank you for your work.
```
