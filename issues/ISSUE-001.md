# ISSUE-001 — New File: concurrent creation can overwrite existing contents

State: Implementing
Authorized-Work: Pull-Request-Implementation
Publication-Target: New-pull-request
External-Reference: Not published.
Contribution-Priority: High
Root-Cause-Confidence: High
Finding-Category: Correctness
Created: 2026-10-11
Updated: 2026-10-11
Source: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`

## Root-Cause

[S] `newFile` checks existence separately from an overwrite-capable creation operation.
This record corresponds to review finding E1.

## Reach-and-Impact

[S] The helper's New File action promises never to overwrite existing files.
[S] Another process creating the same path between the check and write can lose its contents.
Actual macOS reproduction is pending.

## Evidence

- [S] `Sources/FinderMenu/main.swift:204-206` separates `fileExists` from `createFile(contents: nil)`.
- [S] `README.md:17` promises creation without overwriting.
- [S] Apple documents overwrite behavior: https://developer.apple.com/documentation/foundation/filemanager/createfile(atpath:contents:attributes:).
- [S] Foundation provides exclusive creation: https://developer.apple.com/documentation/foundation/nsdata/writingoptions/withoutoverwriting.

## Prior-Art

Coverage: all upstream issues and PRs, including closed states; checked=2026-10-11.
Gaps: release-note bodies and discussion availability remain to be checked.
Issues #1 and #2 concern localization and SMB menu availability, not this creation race.
No upstream PR existed at intake.
Contribution fit: a bounded source fix restoring the explicit no-overwrite contract.

## Proposed-Change

Create the empty file with `Data.write(to:options: .withoutOverwriting)` and preserve collision and write-error dialogs.

## Scope-and-Constraints

- Preserve filename validation, cancellation, Finder selection, and the helper trust boundary.
- Exclude new abstractions, new tests, and unrelated action handling.
- User authorized implementation and direct PR submission for the reviewed findings.

## Verification

- Reproduce the overwrite with a competing file, then verify exclusive creation preserves its contents on macOS.
- Build the helper and extension using the existing build script in a disposable output directory.

## Publication-Blockers

Native reproduction, implementation checks, exact PR draft, and disclosure runtime evidence are pending.

## Next-Action

Summary: Implement exclusive file creation
Action: Implement and verify the scoped Foundation write change on native macOS.
Done-When: The focused reproduction passes and the contribution is ready for submission.

## Pull-Request-Implementation

Branch: fix/issue-001-no-overwrite
Base: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`
Scope: Replace the nonexclusive New File write and preserve its error handling.
Commit: Pending.
Push: Pending.
Checks:

- Pending native macOS verification.
