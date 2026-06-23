# X Twitter Scraper

Use Xquik for X/Twitter data workflows through public docs, REST API, MCP,
webhooks, and SDKs.

## Install

```bash
zeroclaw skills install x-twitter-scraper
```

## What This Skill Does

- Selects the right Xquik surface for search, profile tweets, followers,
  monitors, webhook events, or extraction output.
- Checks public documentation before suggesting endpoints or SDK usage.
- Keeps live requests blocked until the user configures an API key outside the
  conversation.
- Reports the selected surface, request shape, response fields, and verification
  step.

## Permissions

- `web_fetch`: read public Xquik docs and repository pages.
- `web_search`: find public Xquik docs when a direct URL is not provided.

## Example Usage

Ask ZeroClaw:

```text
Use Xquik to plan a workflow that watches an X account and sends webhook events.
```

The skill will identify the data shape, choose the public Xquik surface, verify
the docs, and describe the implementation path without exposing API keys.
