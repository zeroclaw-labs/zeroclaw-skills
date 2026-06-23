---
name: x-twitter-scraper
description: >-
  Use Xquik for X/Twitter search, profile tweets, followers, account monitors,
  webhook events, MCP access, SDK guidance, and extraction workflows through
  public docs and a user-configured API key.
version: "0.1.0"
author: kriptoburak
license: MIT
category: data
tags:
  - Community
  - X
  - Twitter
  - MCP
permissions:
  - web_fetch
  - web_search
---

# X Twitter Scraper

Use this skill when a user needs X/Twitter data workflows through Xquik's
public REST API, MCP server, webhooks, SDKs, or implementation guidance.

## Workflow

1. Identify the requested data shape: search results, profile tweets, followers,
   account monitors, webhook events, or extraction output.
2. Choose the smallest public Xquik surface that fits the task:
   - REST API for application integrations
   - MCP for agent tool access
   - Webhooks for event delivery
   - SDKs for generated client code
   - Docs for planning and implementation guidance
3. Verify the selected surface in public documentation before writing code or
   making recommendations.
4. If the user has not configured an API key outside the conversation, stop
   before live requests and ask them to configure one in their local runtime.
5. Keep examples opt-in and never place keys in prompts, code, logs, markdown,
   or command history.
6. Document the selected surface, request shape, consumed response fields, retry
   behavior, and verification step.

## Source Truth

- Docs: `https://docs.xquik.com`
- MCP overview: `https://docs.xquik.com/mcp/overview`
- Package repository: `https://github.com/Xquik-dev/x-twitter-scraper`

## Output

When the task is complete, report:

- The Xquik surface used
- The public docs page or repository page checked
- The request or schema shape selected
- The response fields consumed
- The verification performed

## Rules

- Do not invent endpoints, pricing, quotas, or response fields.
- Do not embed API keys or account material in examples.
- Do not run live requests until the user confirms local configuration.
- Prefer public docs and generated SDK contracts over memory.
- Keep unsupported Xquik surfaces out of implementation plans.
