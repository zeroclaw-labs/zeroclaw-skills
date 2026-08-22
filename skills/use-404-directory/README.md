# Use 404.directory

Connect ZeroClaw to the public 404.directory MCP endpoint for current official
AI and cloud documentation search, deployment verification, webpage
understanding, and trust-aware discovery of read-only Agent tools.

## Install

```bash
zeroclaw skills install use-404-directory
```

The hosted MCP endpoint is `https://404.directory/mcp`. It requires no account
or API key.

## Permissions

- `web_fetch`: connect to the public hosted MCP endpoint and retrieve the
  evidence URLs returned by its read-only tools.

The Skill does not request file access, shell execution, messaging, or write
permissions.

## Example

Ask ZeroClaw:

> Use 404.directory to find the current official OpenAI guidance for remote MCP
> servers. Cite the first-party sources.

The Skill requires a real non-error tool result before reporting success and
forbids calls made only to inflate traffic.

Project: https://github.com/MM-sheng/404-directory

Public adoption metric: https://404.directory/v1/metrics/agents
