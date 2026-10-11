# ISSUE-006 — Build: independent Swift compilers run sequentially

State: Implementing
Authorized-Work: Pull-Request-Implementation
Publication-Target: New-pull-request
External-Reference: Not published.
Contribution-Priority: Medium
Root-Cause-Confidence: High
Finding-Category: Performance
Created: 2026-10-11
Updated: 2026-10-11
Source: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`

## Root-Cause

[S] `scripts/build.sh` completes extension compilation before starting the independent helper compiler.
This record corresponds to reviewed finding P1.

## Reach-and-Impact

[S] Every source build, including `install.sh`, executes both compilers.
[O] An isolated warm compiler comparison took 3.06 seconds sequentially and 2.14 seconds concurrently.
The comparison excludes packaging, signing, installation, and Finder interaction.

## Evidence

- [S] `scripts/build.sh:19-25` reads shared sources but writes separate executables.
- [S] `scripts/build.sh:29-30` signs the extension before its enclosing app.
- [O] Both compilation orders succeeded on macOS 15.7, arm64, Swift 6.1.2, with Swift language mode 5.

## Prior-Art

Coverage: upstream issue and PR searches for build, compile, parallel, swiftc, icon, render, PNG, and make-icon.
Releases v1.0.0 through v1.2.0 and the upstream root tree were inspected on 2026-10-11.
Discussions are disabled; no contribution guide or PR template exists in the current upstream tree.
Gaps: no exhaustive repository-wide issue-body search is claimed.
The build search returned localization issue #1, which does not own compiler scheduling.
No matching upstream PR was returned.
Contribution fit: one source-only PR changing compiler scheduling without installer or registration changes.

## Proposed-Change

Start exactly two compiler jobs, collect both successful results before signing, and preserve compiler failures.
Keep interruption cleanup bounded to the started jobs and retain extension-before-app signing.

## Scope-and-Constraints

- Preserve Bash 3.2, compiler flags, SDK selection, macOS 13 target, source inputs, bundle paths, and signing order.
- Exclude installer changes, new dependencies, new tests, generated scaffolding, and unrelated cleanup.
- User authorized scoped implementation, fork pushes, and immediate upstream PR submission.
- Parallel compilation may increase peak resource usage; no universal speedup is claimed.

## Verification

- Build and verify signatures in a disposable native macOS output directory without installing the app.
- Exercise compiler failure and interruption without signing incomplete output or leaving owned jobs running.
- Compare full build duration with warm SDK state and record workload and sample limits.

## Performance-Evidence

[O] Earlier component trial: sequential=3.06 seconds; parallel=2.14 seconds; one warm comparison.
The first extension-only compiler trial took 35.60 seconds; its initialization cost was not profiled.
No end-to-end or cold-build improvement is established yet.

## Publication-Blockers

Native verification of the final patch, exact PR draft, contribution commit, and fork push are pending.

## Next-Action

Summary: Implement parallel compiler scheduling
Action: Implement the bounded compiler lifecycle and verify the final script on native macOS.
Done-When: Both executables compile and sign correctly, and failure and interruption checks pass.

## Pull-Request-Implementation

Branch: perf/issue-006-parallel-build
Base: `upstream/main@e172ddfd0e9ddfc2aef78d7c35eba4590b7e50d3`
Scope: Parallelize the two existing compiler commands while preserving failure and signing boundaries.
Commit: Pending.
Push: Pending.
Checks:

- Pending native macOS verification.
