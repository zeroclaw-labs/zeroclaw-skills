---
name: zeroclaw-simplify-review
description: >-
  ZeroClaw-specific simplify preflight. Checks reuse, known project pitfalls, and avoidable complexity before commit or maintainer review.
license: MIT
metadata:
  author: zeroclaw-labs
  version: "0.1.0"
  category: coding
---

# ZeroClaw Simplify Review

Use this skill as a ZeroClaw-specific simplify preflight. It is not a replacement for a general code review skill. Use it before commit, before PR creation, during maintainer review, or when the user asks whether a ZeroClaw change can be simpler, better aligned with existing architecture, or safer against known project pitfalls.

For broad, repository-agnostic code review, use a general code review skill. This skill answers narrower ZeroClaw questions:

- Is the patch using existing ZeroClaw traits, helpers, factories, and crate boundaries?
- Is it avoiding known ZeroClaw failure modes?
- Is the implementation no more complex than the problem requires?
- Is it ready to enter the normal PR/review flow with less review churn?

The default mode is read-only. Do not edit files, post comments, add labels, submit reviews, or otherwise mutate project state unless the user explicitly asks for that action and confirms the specific scope.

## Start

Identify the review target from the user's request, supplied diff, PR context, changed-file list, or specific file paths. Then read the ZeroClaw project instructions that are present in the working tree, especially:

1. `AGENTS.md`
2. `CONTRIBUTING.md`
3. `.github/pull_request_template.md`
4. `docs/book/src/contributing/pr-review-protocol.md`
5. `docs/book/src/maintainers/reviewer-playbook.md`

Only load the docs that exist and are relevant to the target. If the target is small, prefer the nearest source files, tests, and trait/factory wiring over broad documentation reads.

## Review Passes

Run these three passes. If the runtime supports parallel subagents and the user has approved delegation, run them in parallel. Otherwise, run the same passes sequentially.

### Reuse and Architecture

Check whether the change duplicates existing behavior, bypasses traits or factory wiring, adds speculative abstractions, misses an existing helper, or violates ZeroClaw's trait-driven architecture and crate stability tiers.

### Correctness and Quality

Check for bugs, edge cases, stale names, call-site mismatches, state divergence, empty-versus-absent value mistakes, persistence round trips, trait default traps, localization requirements, and missing behavior-first tests.

### Efficiency and Simplicity

Check whether the code is unnecessarily complex, allocates or clones more than needed, adds heavy dependencies, expands runtime footprint, hides side effects, or can be made clearer without changing behavior.

## Aggregation

Merge and de-duplicate the pass results. Lead with findings, ordered by severity:

- **Blocker**: likely bug, security issue, data loss, incorrect public claim, or reviewer-blocking problem.
- **Important**: should be fixed before PR or merge when practical.
- **Suggestion**: optional simplification or polish.

Each finding should include a file and line reference when possible, the reason it matters, and a concrete fix direction. If no issues are found, say that clearly and list any remaining test or validation gaps.

## Rules

- Read changed files and adjacent code before judging the patch.
- Prefer existing ZeroClaw patterns over new abstractions.
- Treat security, privacy, persistence, localization, and public API behavior as higher-risk surfaces.
- Do not nitpick formatting that the formatter or linter owns.
- Keep the output concise enough for a contributor or maintainer to act on.
