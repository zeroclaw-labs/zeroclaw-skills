---
name: codex-cli
description: Ask Codex to explain code in a trusted workspace without requesting edits.
version: "0.1.0"
author: vadelma-agent
license: MIT
category: tools
tags: [Community]
permissions:
  - shell_exec
  - file_read
  - web_fetch
---

Use `codex-cli__ask` for a second opinion, a code explanation, or design trade-offs. ZeroClaw starts Codex in this agent’s configured workspace. Naming another directory in the prompt does not change the working directory or restrict what Codex can read. Name the intended material and any approved files in the prompt shown to the operator. For general questions prefer a neutral workspace and paste only material approved for sharing. For example: “In the public sample, read `parser.rs` and explain why an empty line produces no token. No edits.”

This is an agent run, not a prompt-only API. Codex can run read-only commands and read accessible material; workspace instructions can also become input. File names in the prompt are not an access control. Use trusted inputs and a workspace whose contents are appropriate for this provider; otherwise use an isolated environment or decline. Require ZeroClaw approval for each launch. Report an error or timeout as such; do not switch sandbox, credentials, provider, or tool as a fallback. Do not use this operation to implement changes or as a substitute for a `codex-review` verdict.
