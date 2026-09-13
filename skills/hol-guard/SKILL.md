---
name: hol-guard
description: >-
  Use HOL Guard to protect local AI tool workflows, review Guard approvals and receipts,
  or verify agent skill and MCP packages before use or release.
version: "0.1.0"
author: kantorcodes
license: Apache-2.0
category: security
tags:
  - Community
permissions:
  - shell_exec
---

# HOL Guard

Use HOL Guard when a user wants pre-tool security checks, approval review, audit evidence, or package verification for local AI workflows.

## Rules

- Never bypass a HOL Guard approval.
- Never claim a workspace is protected unless a HOL Guard command proves it.
- Treat scanner failures as real until inspected.
- Prefer HOL Guard-owned commands over manual edits to harness configuration.
- Preserve user changes and do not read `.env` files.

## Check installation

Probe the actual CLIs instead of using shell-specific executable lookup commands:

```bash
hol-guard --version
plugin-scanner --version
```

If HOL Guard is unavailable and the user asked to set it up:

```bash
pipx install hol-guard
```

If package verification is needed and `plugin-scanner` is unavailable:

```bash
pipx install plugin-scanner
```

Then inspect the current Guard state and detect the exact supported harness identifier:

```bash
hol-guard status
hol-guard detect --json
```

## Protect a supported local AI harness

Use the exact harness identifier returned by `hol-guard detect --json`; do not maintain or guess a separate harness list in this skill.

```bash
hol-guard bootstrap
hol-guard install <harness>
hol-guard run <harness> --dry-run
hol-guard run <harness>
hol-guard doctor <harness> --json
hol-guard status
```

Do not claim the session is protected until Guard reports a successful harness setup. A deny, review-required state, Guard error, timeout, or unavailable runtime is not permission to launch an unprotected copy of the agent.

## Handle blocked or review-required work

```bash
hol-guard approvals
hol-guard approvals open <request-id>
hol-guard receipts
hol-guard diff <harness>
```

Use the pending request ID shown by `hol-guard approvals`; do not guess or reuse an unrelated request ID.

Only approve a request after the user has reviewed the risk reason and requested scope.

## Verify an agent skill, plugin, or MCP package

Use the scanner on the package or repository before installation or release:

```bash
plugin-scanner lint <path>
plugin-scanner verify <path>
```

For a ZeroClaw skill, scan the skill directory containing `SKILL.md`. For a mixed agent workspace, scan the repository root so skill, plugin, MCP, and harness configuration surfaces are discovered together.

## Report results

Return the exact command run, what HOL Guard found, anything still blocked or risky, and the evidence or receipt that supports the result. Do not claim protection, approval, or release readiness without command output proving it.
