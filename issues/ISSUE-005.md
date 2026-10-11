# ISSUE-005 — Terminal reset: operational deletion errors are reported as success

State: Implementing
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
Actual native failure reproduction is pending.

## Evidence

- [S] `scripts/finder-menu:65-67` ignores deletion failure and prints the automatic-selection message.
- [S] `Sources/Shared/Settings.swift:25-28` uses an absent value for automatic selection.
- [S] `Sources/FinderMenu/main.swift:150` prioritizes a persisted selection.

## Prior-Art

Coverage: all upstream issues and PRs, including closed states; checked=2026-10-11.
Gaps: release-note bodies and discussion availability remain to be checked.
Issues #1 and #2 concern localization and SMB menu availability, not terminal reset errors.
No upstream PR existed at intake.
Contribution fit: a small CLI error-contract correction if native failure distinction is robust.

## Proposed-Change

Preserve successful reset and absent-key idempotence while returning failure for operational preference errors.

## Scope-and-Constraints

- Preserve command grammar, preferences ownership, and all other terminal selection paths.
- Do not infer absence from any failed preference read.
- Exclude new dependencies, parser machinery, and new tests.
- User authorized implementation and direct PR submission for the reviewed findings.

## Verification

- Inspect native missing-domain, missing-key, and successful deletion behavior in a disposable preference domain.
- Verify the CLI returns nonzero without its success message when a required preference operation fails.

## Publication-Blockers

Robust native missing-key distinction, focused reproduction, exact PR draft, and disclosure runtime evidence are pending.

## Next-Action

Summary: Implement truthful terminal reset
Action: Implement and verify a minimal native reset path preserving absent-key idempotence.
Done-When: Operational failures are visible and the contribution is ready for submission.

## Pull-Request-Implementation

Branch: fix/issue-005-terminal-reset
Base: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`
Scope: Correct the terminal-auto persistence error contract.
Commit: Pending.
Push: Pending.
Checks:

- Pending native macOS verification.
