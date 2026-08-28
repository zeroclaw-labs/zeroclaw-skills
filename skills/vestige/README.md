# vestige

Local-first Rust MCP memory for ZeroClaw.

Causal Backfill answers **"what caused this?"** (shared entities as the join key; similarity excluded from ranking).

Proof: no LLM in the memory path, exact FSRS-6 decay, local-first. Proven on local / synthetic traces. Not a production claim.

```bash
zeroclaw skills install vestige
```

Installing the skill is not enough. ZeroClaw only exposes Vestige tools after you define the MCP server **and** grant it through `mcp_bundles`. Omission is not a grant.

## MCP server package

Official package is vestige-mcp-server on npmjs.

Then resolve the absolute binary path. GUIs do not inherit shell PATH and do not expand tilde.

```bash
which vestige-mcp          # macOS / Linux
where vestige-mcp          # Windows
```
Linux note: wait for v2.4.0 on Ubuntu 22.04 and Debian 12.

## ZeroClaw MCP config

Define the server, then grant it. A mcp.servers row alone does nothing for the agent.
Omission of mcp_bundles is not a grant.
Restart affected sessions after changing bundles.

```toml
[[mcp.servers]]
name = "vestige"
transport = "stdio"
command = "npx"
args = ["-y", "vestige-mcp-server"]

[mcp_bundles.memory]
servers = ["vestige"]

[agents.assistant]
mcp_bundles = ["memory"]
```

Replace assistant with your agent alias.
For GUIs, set command to the absolute path from which vestige-mcp.
Per-project store: pass --data-dir with an absolute directory. There is no --project flag (unknown args exit 1).

Once granted, the agent sees prefixed tools: vestige__recall, vestige__smart_ingest, vestige__backfill.

Optional local SKILL.toml (not shipped here; toml is outside registry file policy) can wrap those MCP tools with kind = mcp and target = vestige__recall (same for smart_ingest and backfill).
kind = mcp targets the prefixed name server__tool. The wrapper still requires the MCP server to be connected through mcp_bundles. If the server is not granted, the wrapper is skipped.

Example local SKILL.toml tools table:

```toml
[skill]
name = "vestige"
description = "Local-first Rust MCP memory with causal Backfill"
version = "0.1.0"
author = "samvallad33"

[[tools]]
name = "recall"
description = "Retrieve memories"
kind = "mcp"
target = "vestige__recall"

[[tools]]
name = "smart_ingest"
description = "Store a durable fact"
kind = "mcp"
target = "vestige__smart_ingest"

[[tools]]
name = "backfill"
description = "Backward causal trail from a later failure"
kind = "mcp"
target = "vestige__backfill"
```

## Advertised tools

| Tool | Arguments | Role |
|------|-----------|------|
| `recall` | `query` (mode `lookup` default) | Retrieve |
| `smart_ingest` | `content` | Store |
| `backfill` | `failure_id`, `manual`, `lookback_days`, `promote`, `scan_limit` | Backward causal trail from a later failure / symptom |

Live Backfill schema: https://github.com/samvallad33/vestige/blob/main/crates/vestige-mcp/src/tools/backfill.rs

## Permissions

- none (`[]`)
  - The skill is agent instructions. Vestige runs as a stdio MCP server spawned by ZeroClaw. Access is gated by mcp_bundles.

## Example usage

- "Remember that I prefer dark mode."
- "What do you remember about the current project?"
- "What caused this outage?"

## Default store

Override with --data-dir (absolute directory only):

- macOS: ~/Library/Application Support/com.vestige.core/
- Linux: ~/.local/share/vestige/core/
- Windows: %APPDATA%\vestige\core\

## Also on ClawHub

This product is already published on ClawHub as https://clawhub.ai/samvallad33/vestige (openclaw skills install @samvallad33/vestige). ZeroClaw can consume ClawHub; this registry entry is the native ZeroClaw skill plus MCP config.

## Credits

- Vestige https://github.com/samvallad33/vestige by @samvallad33
Do not upgrade system glibc.
