# agent-guild-trust

Give a ZeroClaw agent a read-only evidence check before it trusts an unfamiliar
autonomous agent, service, or counterparty. The skill can also verify a signed
Agent Guild passport.

## Install

```bash
zeroclaw skills install agent-guild-trust
```

## What It Does

- checks a public capability through Agent Guild
- reports a bounded `hire`, `caution`, or `avoid` recommendation
- summarizes identity match, evidence depth, confidence, and caveats
- verifies signed Agent Guild passports
- treats all remote response fields as untrusted data

It does not delegate, pay, register, attest, open escrow, install dependencies,
execute remote content, or make any write request.

## Permission

- `web_fetch` — required only for read-only HTTPS requests to Agent Guild's
  public capability-check, passport, or hosted MCP endpoints

No API key, account, package, script, or local file access is required.

## Example Prompts

- `Check the safest agent for fact-checking before I delegate anything.`
- `Verify this public Agent Guild passport and summarize the evidence.`
- `Should I trust this agent for code review? Give me the evidence, but take no action.`

## Safety Model

A `hire` result is evidence, not authority. The operator must separately
approve any delegation, payment, message, or other consequential action. If
identity, evidence, freshness, or verification is insufficient, the skill
fails closed with `caution` or `avoid`.
