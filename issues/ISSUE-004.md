# ISSUE-004 — Parent navigation: case-insensitive comparison matches distinct directories

State: Implementing
Authorized-Work: Pull-Request-Implementation
Publication-Target: New-pull-request
External-Reference: Not published.
Contribution-Priority: Medium
Root-Cause-Confidence: High
Finding-Category: Correctness
Created: 2026-10-11
Updated: 2026-10-11
Source: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`

## Root-Cause

[S] The Finder-window search uses AppleScript's default case-insensitive text equality for filesystem paths.
This record corresponds to review finding E4.

## Reach-and-Impact

[S] On a case-sensitive volume, distinct case-only paths can match and cause navigation of the wrong window.
Actual native comparison and Finder reproduction are pending.

## Evidence

- [S] `Sources/FinderMenu/main.swift:133-135` returns after the first matching path string.
- [S] AppleScript ignores case by default: https://developer.apple.com/library/archive/documentation/AppleScript/Conceptual/AppleScriptLangGuide/reference/ASLR_control_statements.html.

## Prior-Art

Coverage: all upstream issues and PRs, including closed states; checked=2026-10-11.
Gaps: release-note bodies and discussion availability remain to be checked.
Issues #1 and #2 concern localization and SMB menu availability, not case-sensitive window matching.
No upstream PR existed at intake.
Contribution fit: a local AppleScript comparison correction.

## Proposed-Change

Wrap the existing path comparison in `considering case`.

## Scope-and-Constraints

- Preserve matching-window order, fallback navigation, escaping, and automation error logging.
- Exclude new identity abstractions, unrelated path canonicalization, and new tests.
- User authorized implementation and direct PR submission for the reviewed findings.

## Verification

- Reproduce default comparison and verify the corrected comparison using native AppleScript.
- Build on macOS and verify distinct Finder windows on a disposable case-sensitive volume when automation is available.

## Publication-Blockers

Native comparison, Finder automation access, exact PR draft, and disclosure runtime evidence are pending.

## Next-Action

Summary: Implement case-sensitive path comparison
Action: Implement and verify the scoped AppleScript comparison change.
Done-When: Distinct case-only paths no longer match and the contribution is ready for submission.

## Pull-Request-Implementation

Branch: fix/issue-004-case-sensitive-paths
Base: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`
Scope: Make Finder-window path equality consider case.
Commit: Pending.
Push: Pending.
Checks:

- Pending native macOS verification.
