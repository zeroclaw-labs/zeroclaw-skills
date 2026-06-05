---
name: imans
description: Use the Imans CLI to query Imans workspace, catalog, product, variant, and sales order data as JSON.
version: "1.0.0"
author: imans-ai
license: Apache-2.0
category: data
tags: [Community, CLI, Business, Catalog, Sales]
permissions: [shell_exec]
---

# Imans CLI

Use `imans` when a ZeroClaw agent needs read-only Imans workspace, catalog, product variant, sales order, order item, or classification data.

## Setup

- Install: `curl -fsSL https://imans.ai/install | bash`
- Homebrew: `brew install imans-ai/tap/imans`
- Windows Scoop: `scoop bucket add imans https://github.com/imans-ai/scoop-bucket && scoop install imans`
- Verify: `imans version`
- Login interactively: `imans login`
- Login non-interactively: `imans login --token-env IMANS_TOKEN` or `imans login --token-stdin < token.txt`
- Test auth: `imans auth test --quiet`

## Usage Rules

- Use shell execution only to run the `imans` CLI and related safe parsing commands.
- Prefer `--json` for agent parsing.
- Use `--all --json` only when the user asks for full exports.
- Use `--profile <name>` instead of changing the active profile when the user names a workspace.
- Summarize results by default; do not dump large JSON or sensitive order/customer data into chat without confirmation.
- Never print or request raw API tokens.

## Commands

- Workspace: `imans workspace get --json`
- Profiles: `imans profile list`
- Products: `imans products list --all --json`
- Product search: `imans products list --search "<query>" --json`
- Product details: `imans products get <id> --json`
- Variants: `imans product-variants list --product-id <product-id> --all --json`
- Sales orders: `imans sales-orders list --order-date-from <yyyy-mm-dd> --order-date-to <yyyy-mm-dd> --all --json`
- Sales order details: `imans sales-orders get <id> --json`
- Sales order items: `imans sales-order-items list --order-id <order-id> --all --json`
- Classifications: `imans sales-order-classifications list --all --json`

## Safety

- Treat Imans business data as sensitive.
- Prefer `--token-env` or `--token-stdin` over `--token`.
- Exit code `3` means auth failed, `4` means insufficient scope, `5` means not found, `6` means network failure, and `7` means API server error.
