---
name: vestige
description: >-
  Local-first Rust MCP memory. Causal Backfill answers "what caused this?"
  (shared entities as join key; similarity excluded from ranking). Use for
  recall, smart_ingest, and backward-only Backfill when the user wants durable
  local memory or a causal trail after a later failure.
version: "0.1.0"
author: samvallad33
license: MIT
category: research
tags:
  - Community
  - MCP
  - Memory
permissions: []
---

# Vestige Memory

Vestige is local-first Rust MCP memory. Most memory systems answer "what is like this?" Vestige answers **"what caused this?"** via backward-only causal Backfill: shared entities are the join key, and similarity is excluded from ranking.

Proof: no LLM in the memory path, exact FSRS-6 decay, local-first. Proven on local / synthetic traces. Not a production claim.

This skill does not replace ZeroClaw's own files or memory. It sits beside them as an MCP server.

## MCP grant (required)

A `[[mcp.servers]]` entry only *defines* the server. Agents see Vestige tools only when granted through `agents.<alias>.mcp_bundles`. **Omission is not a grant.** An agent with no `mcp_bundles` connects to no MCP servers, even when `mcp.servers` is non-empty.

If `vestige__recall`, `vestige__smart_ingest`, and `vestige__backfill` are missing from the callable tools, stop. Tell the user the Vestige MCP server is not granted to this agent. Do not invent memories. Do not fall back to shelling out to `npx` or `vestige-mcp`.

Prefixed names assume `[[mcp.servers]] name = "vestige"`. If the operator used a different server name, call `{name}__recall`, `{name}__smart_ingest`, `{name}__backfill`.

## Advertised tools

Call these MCP tools. Prefer `recall`. Do not advertise any other Vestige tool as the primary surface.

| Tool | Prefixed name | Arguments | Use |
|------|---------------|-----------|-----|
| `recall` | `vestige__recall` | `query` (mode `lookup` default) | Retrieve |
| `smart_ingest` | `vestige__smart_ingest` | `content` | Store |
| `backfill` | `vestige__backfill` | `failure_id`, `manual`, `lookback_days`, `promote`, `scan_limit` | Start from a later failure / symptom; ranked trail of earlier operational records |

Backfill args follow the live schema in the Vestige repo (`crates/vestige-mcp/src/tools/backfill.rs`). Omit `failure_id` to use the most recent failure-like memory. `manual=true` forces a run. `promote=false` is a dry run.

Optional local `SKILL.toml` may wrap the same tools with `kind = "mcp"` and `target = "vestige__recall"` (and the same for `smart_ingest` / `backfill`). The registry skill is instruction-only; those wrappers are not required.

## When to use

- Causal trail after a later failure ("what caused this?" / "why did this break?") → `vestige__backfill`
- Durable facts worth storing → `vestige__smart_ingest`
- Session recall → `vestige__recall` with `query`
- User preferences, bug fixes, project decisions → `vestige__smart_ingest`

Never save API keys, passwords, or secrets. Never invent stored facts.

## Trigger words

| User says | Action |
|-----------|--------|
| "Remember this" / "Don't forget" | `vestige__smart_ingest` |
| "I always..." / "I never..." / "I prefer..." | `vestige__smart_ingest` as a preference |
| "What caused this?" / "Why did this break?" | `vestige__backfill` |
| "What do you remember about..." | `vestige__recall` |

## Session start

If Vestige tools are granted, recall once at session start:

1. `vestige__recall` with query `user preferences`
2. `vestige__recall` with query `current project context`

If the tools are not granted, skip this and continue without Vestige.
