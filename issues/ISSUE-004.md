# ISSUE-004 — Parent navigation: case-insensitive comparison matches distinct directories

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

[S] The Finder-window search uses AppleScript's default case-insensitive text equality for filesystem paths.
This record corresponds to review finding E4.

## Reach-and-Impact

[S] On a case-sensitive volume, distinct case-only paths can match and cause navigation of the wrong window.
[O] Native AppleScript compared the case-only paths as equal by default and unequal with `considering case`.
Live Finder window AppleEvents timed out with `-1712`; no two-window behavior is claimed as observed.

## Evidence

- [S] `Sources/FinderMenu/main.swift:133-135` returns after the first matching path string.
- [S] AppleScript ignores case by default: https://developer.apple.com/library/archive/documentation/AppleScript/Conceptual/AppleScriptLangGuide/reference/ASLR_control_statements.html.
- [O] Native comparison returned `true` before the fix and `false` with the corrected comparison scope.
- [O] The generated corrected AppleScript compiled successfully on native macOS.
- [O] The existing build script compiled and signed the integrated E1, E3, and E4 Swift changes.

## Prior-Art

Coverage: all upstream issues, PRs, and release notes; checked=2026-10-11.
Gaps: None; upstream discussions are disabled.
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

- [O] Native AppleScript reproduced default equality and verified the corrected inequality.
- [O] The source-derived generated script compiled and the shared native macOS build passed.
- Live Finder two-window verification was unavailable because window AppleEvents timed out with `-1712`.

## Publication-Blockers

None; publication explicitly discloses the live Finder verification limitation.

## Next-Action

Summary: Submit scoped upstream PR
Action: Submit the exact recorded draft to `nikgrbn/finder-menu` with the native comparison and Finder gap disclosed.
Done-When: The upstream PR URL is recorded in this file and the index.

## Pull-Request-Implementation

Branch: fix/issue-004-case-sensitive-paths
Base: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`
Scope: Make Finder-window path equality consider case.
Commit: 1f5c99895c96e0eab7b270c734531a29ba7e837f
Push: origin/fix/issue-004-case-sensitive-paths
Checks:

- Native case-sensitive comparison and generated-script compilation: passed.
- Shared native macOS build and signing: passed.
- Live Finder window actions: unavailable, AppleEvent timeout `-1712`.

## Publication-Draft

Target: https://github.com/nikgrbn/finder-menu, base `main`, head `MikeeI:fix/issue-004-case-sensitive-paths`.
Title: Respect case when matching Finder window directory paths

Body:

```markdown
## Problem

AppleScript ignores case in text comparisons by default: https://developer.apple.com/library/archive/documentation/AppleScript/Conceptual/AppleScriptLangGuide/reference/ASLR_control_statements.html.
The Finder-window search consequently treats `/Projects/A/data/` and `/Projects/a/data/` as equal, although they can identify different directories on a case-sensitive volume.
It updates and returns from the first matching window.
The comparison is visible at https://github.com/nikgrbn/finder-menu/blob/e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3/Sources/FinderMenu/main.swift#L131-L135.

## Change

Wrap the existing path comparison in `considering case`.
Keep matching-window order, path escaping, fallback navigation, and error logging.

## Verification

- On macOS 15.7, native AppleScript returned `true` for the case-only paths by default and `false` inside `considering case`.
- The generated corrected navigation script compiled successfully using native `NSAppleScript`.
- The existing build script compiled and signed an integration build containing the three independent Swift corrections.
- Live Finder two-window behavior remains unverified because window AppleEvents repeatedly timed out with `-1712`.

### Disclosure

Investigated thoroughly with GPT-6.1 Sol (extra high reasoning effort), using [Oh My Pi](https://github.com/can1357/oh-my-pi) as the agent framework.
I reviewed this contribution with GPT-6.1 Sol at xhigh reasoning effort.

This report is not generic or unreviewed AI-generated output.
Its claims were checked against the cited evidence, and it includes the relevant detail intended to help maintainers resolve the issue.

If reports like this are not useful to the project, please let me know and I will refrain from submitting similar ones.
My intent is to help without wasting maintainer time or energy or discouraging their work.

Thank you for your work.
```
