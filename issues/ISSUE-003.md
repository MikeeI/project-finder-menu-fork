# ISSUE-003 — Parent navigation: item targets are mistaken for the browsed directory

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

[S] Parent navigation treats `targetedURL()` as the browsed directory in both item and background menus.
This record corresponds to review finding E3.

## Reach-and-Impact

[S] Right-clicking a child folder passes that child to the helper instead of the containing directory.
[S] The helper computes the already displayed directory as the destination or targets another matching window.
Actual Finder interaction is pending.

## Evidence

- [S] `Sources/FinderMenuExtension/FinderSync.swift:13,82-90` shares the target between two menu contexts.
- [S] `Sources/FinderMenu/main.swift:113,127-139` retains directory targets and computes their parent.
- [S] The target is the clicked item: https://developer.apple.com/documentation/findersync/fifindersynccontroller/targetedurl().

## Prior-Art

Coverage: all upstream issues and PRs, including closed states; checked=2026-10-11.
Gaps: release-note bodies and discussion availability remain to be checked.
Issues #1 and #2 concern localization and SMB menu availability, not navigation targeting.
No upstream PR existed at intake.
Contribution fit: a bounded action-specific context correction.

## Proposed-Change

Use the containing directory for parent navigation from an item menu and the target directory for a background menu.
Apply the root check to that same navigation directory.

## Scope-and-Constraints

- Preserve terminal, editor, copy-path, and New File targeting.
- Preserve Finder menu-copy behavior and use the existing menu-tag convention when context must survive copying.
- Exclude universal Finder-window identification, virtual-view redesign, and new tests.
- User authorized implementation and direct PR submission for the reviewed findings.

## Verification

- Build on native macOS and verify item/background navigation and the root guard.
- Reproduce with a child-folder context menu while a separate Finder window displays the child.

## Publication-Blockers

Finder automation access, native reproduction, exact PR draft, and disclosure runtime evidence are pending.

## Next-Action

Summary: Implement navigation context handling
Action: Implement and verify action-specific Finder navigation targeting.
Done-When: Item and background menus navigate from the correct directory and the contribution is ready for submission.

## Pull-Request-Implementation

Branch: fix/issue-003-navigation-context
Base: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`
Scope: Correct the parent-folder menu context and corresponding root guard.
Commit: Pending.
Push: Pending.
Checks:

- Pending native macOS verification.
