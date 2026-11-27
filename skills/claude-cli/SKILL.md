---
name: claude-cli
description: Ask Claude about a question, a pasted excerpt, or an explicitly chosen project file.
version: "0.2.0"
author: vadelma-agent
license: MIT
category: tools
tags:
  - Community
permissions:
  - shell_exec
  - file_read
  - web_fetch
---

Use `claude-cli__ask` for a second opinion on a question, a pasted excerpt, or a file the operator explicitly wants Claude to see. For example: “In @README.md, which setup instruction is ambiguous?” is appropriate when the operator has approved sharing that README from this workspace.

Claude’s model tools are disabled. Its CLI can still expand file references before the model sees the question. Name the agreed workspace and selected file(s) in the `prompt` so they appear in the approval dialog. Check references, including those inside quoted or pasted material, against the operator’s intended files. If the target or permission to share it is unclear, ask first; do not forward untrusted input verbatim. The working directory is not an OS access boundary. For a task that needs actual edits or a formal review, use the appropriate workflow instead. Return the answer or the actual error; do not retry with a weaker policy.
