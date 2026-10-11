# ISSUE-007 — Icon generator: identical pixel sizes render and encode repeatedly

State: Implementing
Authorized-Work: Pull-Request-Implementation
Publication-Target: New-pull-request
External-Reference: Not published.
Contribution-Priority: Low
Root-Cause-Confidence: High
Finding-Category: Performance
Created: 2026-10-11
Updated: 2026-10-11
Source: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`

## Root-Cause

[S] The README image and iconset request eleven PNG encodes for seven distinct pixel sizes.
This record corresponds to reviewed finding P3.

## Reach-and-Impact

[S] The repeated work occurs only when running `swift scripts/make-icon.swift`.
[O] Earlier in-memory trials measured median rendering and encoding of 55.56 ms versus 37.03 ms.
This is a small developer-tool optimization, not a Finder runtime improvement.

## Evidence

- [S] `scripts/make-icon.swift:87-93` requests 32 and 256 pixels twice and 512 pixels three times.
- [S] `scripts/make-icon.swift:5-84` draws fixed geometry and colors for each pixel size.
- [O] Repeated PNG outputs were byte-equal on macOS 15.7 with Swift 6.1.2.

## Prior-Art

Coverage: upstream issue and PR searches for icon, render, PNG, make-icon, build, and parallel.
Releases v1.0.0 through v1.2.0 and the upstream root tree were inspected on 2026-10-11.
Discussions are disabled; no contribution guide or PR template exists in the current upstream tree.
Gaps: no exhaustive repository-wide issue-body search is claimed.
The v1.1.0 release fixed Finder menu image-copy size, a distinct runtime root cause.
No matching generator issue or PR was returned.
Contribution fit: one small source-only PR reusing PNG bytes within a generator invocation.

## Proposed-Change

Reuse encoded PNG Data by pixel size only within the current invocation.
Preserve every output filename, write order, drawing function, and iconutil invocation.

## Scope-and-Constraints

- Keep the cache limited to seven requested sizes and the lifetime of one process invocation.
- Preserve existing file-write and encoding failure behavior.
- Exclude drawing changes, regenerated assets in the PR, new tests, and unrelated resource cleanup.
- User authorized scoped implementation, fork pushes, and immediate upstream PR submission.

## Verification

- Run upstream and patched generators in separate disposable native macOS source trees.
- Compare README PNG, all iconset PNGs, and the resulting AppIcon.icns byte-for-byte.
- Measure the full generator separately from rendering-only timing.

## Performance-Evidence

[O] Earlier component comparison used twelve samples per variant and eleven requests for seven sizes.
Median rendering and encoding decreased from 55.56 ms to 37.03 ms.
File writes, Swift startup, and iconutil were not part of that comparison.

## Publication-Blockers

Native full-output comparison, exact PR draft, contribution commit, and fork push are pending.

## Next-Action

Summary: Reuse repeated icon PNGs
Action: Implement per-invocation PNG reuse and compare complete native generator outputs.
Done-When: All output bytes match upstream and the source-only contribution is ready for submission.

## Pull-Request-Implementation

Branch: perf/issue-007-reuse-icon-png
Base: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`
Scope: Reuse PNG Data for repeated pixel sizes during one icon-generator invocation.
Commit: Pending.
Push: Pending.
Checks:

- Pending native macOS verification.
