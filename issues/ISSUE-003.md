# ISSUE-003 — Parent navigation: item targets are mistaken for the browsed directory

State: PR-Ready
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
[O] Native AppKit smoke verified the corrected item/background paths, root path, and copied menu tag.
Live Finder interaction was unavailable: window AppleEvents repeatedly timed out with error `-1712`.

## Evidence

- [S] `Sources/FinderMenuExtension/FinderSync.swift:13,82-90` shares the target between two menu contexts.
- [S] `Sources/FinderMenu/main.swift:113,127-139` retains directory targets and computes their parent.
- [S] The target is the clicked item: https://developer.apple.com/documentation/findersync/fifindersynccontroller/targetedurl().
- [O] Source-derived native AppKit smoke returned correct item/background paths and retained the archived menu tag.
- [O] The existing build script compiled and signed the integrated E1, E3, and E4 Swift changes.

## Prior-Art

Coverage: all upstream issues, PRs, and release notes; checked=2026-10-11.
Gaps: None; upstream discussions are disabled.
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

- [O] Native source-derived AppKit smoke verified item/background paths, root suppression, and menu-tag transport.
- [O] The shared native macOS build and signing passed.
- Live Finder two-window verification could not run because Finder window AppleEvents timed out with `-1712`.
- The smoke substituted the target input and did not claim a live Finder context-menu reproduction.

## Publication-Blockers

None; publication explicitly discloses the live Finder verification limitation.

## Next-Action

Summary: Submit scoped upstream PR
Action: Submit the exact recorded draft to `nikgrbn/finder-menu` with the native smoke and live Finder gap disclosed.
Done-When: The upstream PR URL is recorded in this file and the index.

## Pull-Request-Implementation

Branch: fix/issue-003-navigation-context
Base: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`
Scope: Correct the parent-folder menu context and corresponding root guard.
Commit: bbea7c2001c8576ed92c7ab8382b9426ce634bdf
Push: origin/fix/issue-003-navigation-context
Checks:

- Native source-derived AppKit context and copied-tag smoke: passed.
- Shared native macOS build and signing: passed.
- Live Finder window actions: unavailable, AppleEvent timeout `-1712`.

## Publication-Draft

Target: https://github.com/nikgrbn/finder-menu, base `main`, head `MikeeI:fix/issue-003-navigation-context`.
Title: Use the browsed directory for parent navigation from item menus

Body:

```markdown
## Problem

`targetedURL()` identifies the clicked item, not necessarily the directory displayed by the Finder window.
Parent navigation currently treats it as the displayed directory in both item and background menus.
Right-clicking `/Projects/Build` while browsing `/Projects` therefore computes `/Projects` as the destination instead of navigating above it.
If another window displays the child folder, the helper can select that matching window instead.

The extension's current targeting is visible at https://github.com/nikgrbn/finder-menu/blob/e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3/Sources/FinderMenuExtension/FinderSync.swift#L82-L90.
The API contract is documented at https://developer.apple.com/documentation/findersync/fifindersynccontroller/targetedurl().

## Change

For parent navigation, use the containing directory in an item menu and the targeted directory in a background menu.
Retain the menu kind in the existing copied-tag channel and use the same normalized directory for the root guard.
Do not alter terminal, editor, New File, or copy-path targeting.

## Verification

- On macOS 15.7 arm64, a source-derived AppKit smoke verified item/background paths, the root case, and retention of the tag through native menu-item archiving.
- The existing build script compiled and signed an integration build containing the three independent Swift corrections.
- Live Finder context-menu and two-window behavior remain unverified: Finder window AppleEvents repeatedly timed out with `-1712`.
- The smoke substituted the target input; it was not an end-to-end Finder reproduction.

### Disclosure

Investigated thoroughly with GPT-6.1 Sol (extra high reasoning effort), using [Oh My Pi](https://github.com/can1357/oh-my-pi) as the agent framework.
I reviewed this contribution with GPT-6.1 Sol at xhigh reasoning effort.

This report is not generic or unreviewed AI-generated output.
Its claims were checked against the cited evidence, and it includes the relevant detail intended to help maintainers resolve the issue.

If reports like this are not useful to the project, please let me know and I will refrain from submitting similar ones.
My intent is to help without wasting maintainer time or energy or discouraging their work.

Thank you for your work.
```
