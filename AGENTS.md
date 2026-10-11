# Repository Guidelines

<essential-rule>
AGENTS.md is the sole authoritative project context file.
Read and edit AGENTS.md directly.
</essential-rule>

## Project Overview

Finder Menu is a Swift macOS Finder Sync extension and helper app for file creation, terminal/editor launching, path copying, and parent-folder navigation.
The upstream build targets macOS 13.0 and uses direct `swiftc` compilation, not Swift Package Manager or an Xcode project.

## Fork & Upstream Contribution Intent

- Official upstream: [nikgrbn/finder-menu](https://github.com/nikgrbn/finder-menu).
- This checkout is the [MikeeI/project-finder-menu-fork](https://github.com/MikeeI/project-finder-menu-fork) fork, not an independently owned product.
- The goal is to support upstream with evidence-backed issues, comments, and pull requests.
- `ISSUES.md` provides the compact finding overview and global ID allocator.
- `issues/ISSUE-NNN.md` owns the complete durable record for one root cause.
- `FORMAT.md` owns research, drafting, implementation authorization, approval, and publication rules.
- Prefer small, well-scoped corrections with outsized maintainer or user value.
- Recommend a pull request when a bounded verified fix is ready and no active implementation owns it.
- Otherwise recommend a comment, a new issue, or continued investigation according to the evidence.
- Apply `skill-fork-contribution-tracking` for ledger, lifecycle, personal-branch, and upstream handoff work.
- Apply `skill-maintainer-communication` before external issues, pull requests, reviews, comments, or discussions.
- Search existing work first and follow current upstream templates and disclosure rules.
- Apply `skill-semantic-compression-3` when authoring or restructuring tracking content.
- Apply `skill-git-commit-format` while respecting explicit upstream contribution conventions.
- Never choose `Authorized-Work` or `Publication-Target` on the user's behalf.
- `Research-and-Reporting` permits research, issues, and comments but no source implementation.
- `Pull-Request-Implementation` authorizes only the scoped implementation recorded for that finding.
- Base upstream contribution branches on current `upstream/main`.
- Keep fork-only context, ledgers, configuration, and personal commits out of upstream contribution diffs.
- Reproduce claimed bugs against current upstream and run the narrowest conclusive verification.
- Publish one coherent root cause per issue, comment, or pull request.

## Finding and Contribution Ledger

- At the start of every agent session, agents MUST read root `ISSUES.md` before repository work.
- `ISSUES.md` owns the global `Next finding ID` allocator and compact cross-finding overview.
- Each `issues/ISSUE-NNN.md` owns one finding's state, evidence, drafts, and next action.
- `FORMAT.md` is authoritative for research, drafting, implementation boundaries, and publication format.
- Before adding a finding, search the index and every relevant issue record for the same symptom or root cause.
- New findings MUST use `Next finding ID`.
- Create the issue file, add its index row, and increment the allocator together.
- Finding IDs use `ISSUE-NNN`, start at `ISSUE-001`, and remain permanent.
- Never reuse, renumber, or scope IDs by subsystem, status, session, or contribution type.
- Update the issue file and `ISSUES.md` together after state, authorization, target, priority, next action, or reference changes.
- Every issue record MUST use the field and section contract in `FORMAT.md`.
- New findings start with `State: Investigating`, `Authorized-Work: Not-Selected`, and `Publication-Target: Not-Selected`.
- New findings use `External-Reference: Not published.` until an external reference exists.
- Keep findings Investigating until currentness, prior art, impact, and correction value are evidence-backed.
- Clone detectors, AST matches, text similarity, shared names, and TODOs produce candidates only.
- A duplication finding requires shared change pressure, realistic drift, and simpler consolidation.
- The user selects `Authorized-Work` for each finding.
- `Research-and-Reporting` MUST NOT implement the finding.
- `Pull-Request-Implementation` MAY implement only the recorded scope after research resolves callers and failure modes.
- Pull-request work MUST verify behavior, commit, push, and reach `PR-Ready` before publication.
- Show the exact draft and target before publishing an issue, comment, or pull request to official upstream.
- Publish to official upstream only after the user approves the exact current draft and target.
- Any draft or target change requires showing the complete current draft and target again before publication.
- Run the read-only validator bundled with `skill-fork-contribution-tracking` after every ledger mutation.
- Record the final external URL in `External-Reference` immediately after publication.
- Keep `FORMAT.md`, `ISSUES.md`, `issues/`, and fork-only `AGENTS.md` changes out of upstream contribution diffs.

### External publication approval

Only an external issue, comment, review, discussion, or pull request write is approval-gated.
Before publication, read current contribution guidance and explain applicable project policy.
The human must be able to review and own every submission statement.
Fork commits, pushes, tracking updates, and source implementation follow the active repository contract.

## Branch Roles

- Local workspace: `/root/projects/project-finder-menu-fork`.
- `origin` is the personal GitHub fork; `upstream` is the canonical source repository.
- `personal` owns fork-only guidance and contribution tracking and tracks `origin/personal`.
- `main` remains the clean upstream-derived branch; never merge `personal` into an upstream contribution branch.
- Start each contribution branch or worktree from the current `upstream/main`, not `personal`.

## Architecture & Data Flow

- `Sources/FinderMenuExtension/FinderSync.swift` builds Finder menus, copies paths, and dispatches helper actions.
- The sandboxed extension sends `findermenu://` URLs to the helper for actions outside its sandbox.
- `Sources/FinderMenu/main.swift` owns URL handling, sender validation, file creation, app launching, and Finder navigation.
- `Sources/FinderMenu/SettingsWindow.swift` owns the settings UI; `Terminals.swift` owns terminal candidates.
- `Sources/Shared/Settings.swift` owns menu identifiers and preferences in `local.findermenu`.
- The helper writes preferences; the extension reads them through its shared-preference entitlement.
- `Sources/Shared/Editors.swift` owns the built-in editor list shared by the extension and helper.

## Key Directories and Important Files

- `Sources/`: helper, Finder extension, and shared Swift source.
- `Resources/`: bundle metadata, sandbox entitlements, and app icon.
- `scripts/`: compilation, signing, extension lifecycle, settings CLI, and icon generation.
- `docs/`: screenshots and menu icons used by the upstream README.
- `README.md`: upstream installation, settings, architecture, and troubleshooting guidance.
- `SECURITY.md`: private reporting policy for vulnerabilities in current upstream `main`.

## Development Commands

Run application commands only on macOS with the Xcode command-line tools installed:

- Build without installation: `scripts/build.sh /absolute/temporary/output-directory`.
- Build, install, and enable the extension: `./install.sh`.
- Disable and uninstall: `./uninstall.sh`.
- Open installed settings: `scripts/finder-menu settings`.
- Show or select a terminal: `scripts/finder-menu terminal [app]`; use `auto` to reset.
- Regenerate icons: `swift scripts/make-icon.swift`.

## Code Conventions and Runtime/Tooling Preferences

- Preserve upstream Swift/AppKit/FinderSync conventions and the existing Bash build/install workflow.
- Compilation uses Swift language mode 5, optimization, the active macOS SDK, and the host architecture.
- Do not replace upstream tooling with generated project scaffolding, package manifests, or unrelated quality commands.
- Preserve sender code-signature validation and the helper/extension sandbox boundary when changing action handling.
- `scripts/build.sh` removes its output app bundle before rebuilding; use a disposable output directory.
- `install.sh` builds outside the checkout to prevent macOS from registering a second extension copy.
- Installation replaces the installed app and interacts with Finder extension registration; do not use it as a read-only check.
- Preserve the inherited upstream MIT `LICENSE`.

## Testing & QA

- The initial upstream tree has no automated test suite or CI workflow; do not invent a passing test gate.
- Build/signing and Finder integration require macOS; Linux cannot verify the actual application surface.
- Verify source changes on macOS at the narrowest affected boundary, including Finder menu actions when applicable.
- For ledger changes, run `bun /root/projects/project-settings-omp/data/sync/agent/skills/skill-fork-contribution-tracking/scripts/validate-ledger.js /root/projects/project-finder-menu-fork`.
- Report build or Finder interaction as unverified when no macOS runtime is available.

## Development Rules

Before launching agents, apply skill-xray, skill-expert, and skill-brutal to the task.
Surface expert-level issues, non-obvious issues, blindspots, stale assumptions, and hidden dependencies.
Also surface missed constraints, edge cases, false positives, verification gaps, overclaims, and weak assumptions.
Identify improvement potential, inefficiencies, and what is wrong without softening.
Use these findings to design safe slices, sequencing, checks, and boundaries for complete agent results.

Every agent prompt must require skill-xray, skill-expert, and skill-brutal for the assigned scope before acting.
It must surface non-obvious issues, blindspots, stale assumptions, hidden dependencies, and edge cases.
It must also surface verification gaps, overclaims, failure modes, weak assumptions, and what is wrong.
The agent must adjust its approach, challenge its assumptions, and flag misleading or incomplete output risks.

Implementation assignments must cover existing patterns, callers, exported-symbol consumers, and failure modes.
They must also cover concurrency safety and lifecycle cleanup.
Each assignment must state `Test decision: none` or `Test decision: update`.
`update` must name the exact existing test that follows an intentional contract change.
Never request new tests.
Prohibit broad edits, unrelated cleanup, and unassigned files.

No vague agents.
Each assignment needs exact targets, non-goals, evidence anchors, acceptance criteria, and an output contract.

Commit completed units continuously.
Before each commit, use skill-git-commit-format to determine whether staged effects are one coherent unit.
The skill owns commit-message format and evidence.
After the boundary is valid, run the repository-owned commit and push workflow.
Do not commit every trivial edit immediately or defer unrelated work into one end-of-session commit.

Every project-level quality command is quiet by default and verbose on demand.
This policy applies regardless of language or toolchain.
It covers Make targets, package scripts, Python CLIs, shell quality gates, and test runners.
Successful checks print only compact status such as `format: ok`, `lint: ok`, `test: ok`, or `check: ok`.
On failure, exit non-zero and print the failing step, exit code, and enough output to act without rerunning.
Full raw output must remain available through `--verbose`, `VERBOSE=1`, or the underlying tool's verbose mode.
New quality commands and future language setup must follow this policy instead of inventing another logging contract.
