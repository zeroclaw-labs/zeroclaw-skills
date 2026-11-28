---
name: claude-cli
description: Ask the installed Claude Code CLI a question in a trusted workspace using its configured tools and permissions.
version: "0.3.0"
author: vadelma-agent
license: MIT
category: tools
tags:
  - Community
permissions:
  - shell_exec
  - file_read
  - file_write
  - web_fetch
---

Use `claude-cli__ask` for a question about a trusted checkout. In the prompt, name the task and material the operator is willing to share. Confirm that the actual working directory and its Claude Code configuration are trusted. Example: “In this approved sample checkout, explain why the parser returns an empty list. Cite the implementation. Do not edit files.” That last sentence is an instruction, not an access control.

This approves a Claude Code run, not a text-only exchange. Project hooks and MCP servers can start without a separate prompt. Claude may use its configured tools, edit files, expand file references, and send project material to a provider. Do not pass untrusted workspaces or third-party requests to a native run. Return the result or actual error; do not retry with a weaker policy.
