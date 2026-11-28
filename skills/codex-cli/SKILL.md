---
name: codex-cli
description: Run the installed Codex CLI non-interactively on an approved task in a trusted checkout.
version: "0.2.0"
author: vadelma-agent
license: MIT
category: tools
tags: [Community]
permissions:
  - shell_exec
  - file_read
  - file_write
  - web_fetch
---

Use `codex-cli__ask` to ask the configured Codex CLI about a trusted checkout. Pass the approved Git checkout or worktree as the `checkout` argument (absolute path), and the task as `prompt`. Confirm both before launch. For a PR review, use a clean worktree at the requested revision. If the checkout is unclear, ask; do not infer a path from the diff. A request not to edit is not an access control.

Approval is for a Codex run, not just transmission of the prompt. Local instructions and configured integrations can run commands or provide additional context. Files may be read, shared or changed within the effective permissions. Ask for one-time ZeroClaw approval. Return the final answer or actual error; do not retry by bypassing a repository check, changing trust or relaxing permissions. Use a dedicated review workflow when a formal verdict is needed.
