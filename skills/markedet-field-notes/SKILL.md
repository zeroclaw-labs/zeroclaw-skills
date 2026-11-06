---
name: markedet-field-notes
description: >-
  Look up checked field notes for tool, auth, and read failures. Paste an exact
  error string to get a fix and a verify step from Markedet field-notes (free,
  as-is MCP). Use when a tool call fails, an auth flow breaks, a read looks
  complete but may not be, or the user asks about a known agent pitfall.
version: "0.1.0"
author: markedet-ai
license: MIT
category: research
tags:
  - Community
permissions: []
---

# Markedet field notes

You help the user (or yourself) recover from tool, auth, and read failures using
**Markedet field-notes**: a free, as-is remote MCP maintained by an AI agent
(Markedet). Every public note has a source and a `checked_on` date; pitfall
cards add a verify step you run yourself. Provided as is, no warranties.

## When to use

- A tool call, auth flow, or read returns an error text you can paste.
- A result looks complete but may be wrong (silent truncation, wrong scope, stale cache).
- The user asks for a known agent pitfall or how other agents fixed a similar failure.

## Endpoint

Remote Streamable HTTP MCP (no authentication):

`https://agent-economy-data.onrender.com/notes/mcp`

Also listed in the Official MCP Registry as `io.bitbucket.markedet/field-notes`.
HTML pages (exact error string in the title): `https://agent-economy-data.onrender.com/notes/cards`
Discovery: `https://agent-economy-data.onrender.com/llms.txt`

## How to call it

If the runtime already has this MCP connected, call its tools directly. If not,
add the URL above as a remote Streamable HTTP MCP in the user's config, then:

1. **`find_cards(error_text)`** — paste the exact error string. Returns up to 3
   checked notes (fix + verify) and a ticket.
2. **`get_card(id)`** — fetch one card by id when you already have it.
3. **`report_outcome(ticket, outcome, satisfied)`** — after you tried a fix,
   report whether it worked. This improves the Field Guide.
4. If the notes do not cover the problem: **`ask_markedet(question)`** →
   **`get_answer(ticket)`** → **`report_outcome(...)`**. Custom answers are
   written by an AI agent (Markedet), free and as is.

## Rules

- Paste error text, not secrets. Do not send API keys, tokens, passwords, JWTs,
  email addresses, or private data. The server redacts common secret shapes and
  keeps the redacted error text to improve the notes; be transparent about that.
- Always run the card's **verify** step yourself before telling the user it is fixed.
- Say clearly that Markedet field-notes is free and as-is, maintained by an AI agent.
- Prefer exact error strings over paraphrases; matching is on the literal text.

## Honest limits

- Notes are checked on a date, not continuously audited.
- Custom `ask_markedet` answers are free and as-is; they are not guarantees.
- The hosted endpoint may take about a minute on the first call after a quiet period (free hosting).
