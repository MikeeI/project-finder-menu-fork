# ISSUE-001 — New File: concurrent creation can overwrite existing contents

State: Submitted
Authorized-Work: Pull-Request-Implementation
Publication-Target: New-pull-request
External-Reference: https://github.com/nikgrbn/finder-menu/pull/3
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
[O] On macOS 15.7 arm64, the old write reduced competing contents to zero bytes.

## Evidence

- [S] `Sources/FinderMenu/main.swift:204-206` separates `fileExists` from `createFile(contents: nil)`.
- [S] `README.md:17` promises creation without overwriting.
- [S] Apple documents overwrite behavior: https://developer.apple.com/documentation/foundation/filemanager/createfile(atpath:contents:attributes:).
- [S] Foundation provides exclusive creation: https://developer.apple.com/documentation/foundation/nsdata/writingoptions/withoutoverwriting.
- [O] Native source-derived smoke returned `baseline_bytes: 0`, `collision: preserved`, and `fresh_file: empty`.
- [O] The existing build script compiled and signed the integrated E1, E3, and E4 Swift changes.
- [O] Session metadata verified `openai-codex/gpt-6.1-sol` and effective `xhigh` reasoning before publication.

## Prior-Art

Coverage: all upstream issues, PRs, and release notes; checked=2026-10-11.
Gaps: None; upstream discussions are disabled.
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

- [O] Native Foundation smoke reproduced content loss and verified exclusive creation and collision handling.
- [O] `scripts/build.sh /tmp/finder-menu-review-20261011/output` passed on macOS 15.7 arm64.
- The Finder dialog and selection surface were not exercised; the changed file-write contract was verified directly.

## Publication-Blockers

None.

## Next-Action

Summary: Monitor upstream PR feedback
Action: Review maintainer feedback on https://github.com/nikgrbn/finder-menu/pull/3.
Done-When: Feedback is addressed or an evidence-backed merge or closure disposition is recorded.

## Pull-Request-Implementation

Branch: fix/issue-001-no-overwrite
Base: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`
Scope: Replace the nonexclusive New File write and preserve its error handling.
Commit: 927caf0bdb05ba13313f9d13a1282ad839e6d311
Push: origin/fix/issue-001-no-overwrite
Checks:

- Native Foundation overwrite and exclusive-creation smoke: passed.
- Shared native macOS build and signing: passed.

## Publication-Draft

Target: https://github.com/nikgrbn/finder-menu, base `main`, head `MikeeI:fix/issue-001-no-overwrite`.
Title: Prevent New File from overwriting files created concurrently

Body:

```markdown
## Problem

New File promises never to overwrite an existing file, but its existence check and creation are separate operations.
If a file appears between them, `FileManager.createFile(atPath:contents: nil)` overwrites its contents.
The current sequence is visible at https://github.com/nikgrbn/finder-menu/blob/e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3/Sources/FinderMenu/main.swift#L204-L206.

## Change

Use `Data.write(to:options: .withoutOverwriting)` as the exclusive creation operation.
Preserve the existing collision message, other write-error message, filename validation, cancellation, and Finder selection.

## Verification

- On macOS 15.7 arm64 with Swift 6.1.2, an isolated native reproduction placed nonempty contents after the absence check and before the old creation call.
- The old call reduced the file to zero bytes; the new call raised `fileWriteFileExists` and preserved the contents.
- A genuinely new file was created successfully with empty contents.
- The existing build script compiled and signed an integration build containing the three independent Swift corrections.
- The Finder dialog and selection surface were not exercised; verification covered the changed file-write contract directly.

### Disclosure

Investigated thoroughly with GPT-6.1 Sol (extra high reasoning effort), using [Oh My Pi](https://github.com/can1357/oh-my-pi) as the agent framework.
I reviewed this contribution with GPT-6.1 Sol at xhigh reasoning effort.

This report is not generic or unreviewed AI-generated output.
Its claims were checked against the cited evidence, and it includes the relevant detail intended to help maintainers resolve the issue.

If reports like this are not useful to the project, please let me know and I will refrain from submitting similar ones.
My intent is to help without wasting maintainer time or energy or discouraging their work.

Thank you for your work.
```
