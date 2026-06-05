# Imans CLI Skill for ZeroClaw

This skill teaches ZeroClaw agents how to use the official `imans` CLI for read-only Imans workspace, catalog, and sales order workflows.

## Install

```bash
zeroclaw skills install imans
```

## Runtime Requirement

The `imans` binary must be available where ZeroClaw executes shell commands.

```bash
curl -fsSL https://imans.ai/install | bash
imans version
imans login
imans auth test --quiet
```

## Permissions

- `shell_exec`: required to run the `imans` CLI.

The skill does not require write permissions to the Imans API. The current CLI command surface is read-only for workspace, catalog, and sales order resources.

## Example Prompts

- "Show me the active Imans workspace."
- "Find products matching shirt in Imans."
- "Summarize sales orders from 2026-04-01 to 2026-04-30."
- "Get the line items for sales order 456."
