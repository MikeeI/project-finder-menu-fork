# ISSUE-007 — Icon generator: identical pixel sizes render and encode repeatedly

State: PR-Ready
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

- [O] Both actual generators completed successfully in separate disposable source trees on macOS 15.7.
- [O] README PNG, all ten iconset PNGs, and AppIcon.icns were byte-identical.
- [O] Full invocations took 2.00 and 0.50 seconds; unequal startup/cache state prevents a speedup claim.

## Performance-Evidence

[O] Earlier component comparison used twelve samples per variant and eleven requests for seven sizes.
Median rendering and encoding decreased from 55.56 ms to 37.03 ms.
File writes, Swift startup, and iconutil were not part of that comparison.

## Publication-Blockers

None.

## Next-Action

Summary: Submit icon reuse PR
Action: Submit the verified source-only contribution to nikgrbn/finder-menu main.
Done-When: The external PR URL is recorded and the ledger is validated and pushed.

## Pull-Request-Implementation

Branch: perf/issue-007-reuse-icon-png
Base: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`
Scope: Reuse PNG Data for repeated pixel sizes during one icon-generator invocation.
Commit: 8f3cb356a626abde75de6e3b8b83847c158a909a
Push: origin/perf/issue-007-reuse-icon-png
Checks:

- `swift scripts/make-icon.swift` in separate disposable source trees → both actual generators succeeded.
- `cmp` for README PNG and AppIcon.icns → identical bytes.
- `diff -rq` for baseline and patched iconsets → all ten PNGs identical.
- Foundation used the native per-user temp directory despite TMPDIR; the iconset was snapshotted before rerunning upstream.
- `git diff --name-only upstream/main...HEAD` → only `scripts/make-icon.swift`.

## Publication-Draft

Target: https://github.com/nikgrbn/finder-menu, base=main, head=MikeeI:perf/issue-007-reuse-icon-png
Title: Reuse PNG data for repeated icon sizes

Body:

```markdown
## Summary

The README image and iconset request eleven PNGs but use only seven distinct pixel sizes.
Reuse encoded PNG data by pixel size within the current generator invocation.
Keep drawing code, output filenames, write order, and the `iconutil` invocation unchanged.
The cache retains encoded bytes rather than bitmap representations and does not persist across runs.

## Verification

- Ran the upstream and patched generators on macOS 15.7 with Swift 6.1.2.
- Compared the README PNG, all ten iconset PNGs, and the final AppIcon.icns byte-for-byte.
- All output bytes matched.
- The contribution changes only the generator source, not generated assets.

## Performance

An earlier rendering-and-encoding comparison used twelve samples per variant.
Its medians were 55.56 ms without reuse and 37.03 ms with reuse.
That component comparison excluded file writes, Swift startup, and `iconutil`.
This is a small developer-tool optimization and does not change Finder runtime performance.

### Disclosure

Investigated thoroughly with GPT-6.1 Sol (extra high reasoning effort), using [Oh My Pi](https://github.com/can1357/oh-my-pi) as the agent framework.
I reviewed this contribution with GPT-6.1 Sol at xhigh reasoning effort.

This report is not generic or unreviewed AI-generated output.
Its claims were checked against the cited evidence, and it includes the relevant detail intended to help maintainers resolve the issue.

If reports like this are not useful to the project, please let me know and I will refrain from submitting similar ones.
My intent is to help without wasting maintainer time or energy or discouraging their work.

Thank you for your work.
```
