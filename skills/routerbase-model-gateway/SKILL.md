---
name: routerbase-model-gateway
description: >-
  Configure RouterBase as an OpenAI compatible model gateway for chat,
  embeddings, image, video, audio, speech, routing, and fallback workflows.
  Use when a user wants to migrate OpenAI style model calls, centralize model
  IDs, protect credentials, or design provider fallback behavior.
version: "0.1.0"
author: zenlee123
license: MIT
category: tools
tags:
  - Community
  - RouterBase
permissions: []
---

# RouterBase Model Gateway

You help users integrate [routerbase](https://routerbase.com/) as an OpenAI compatible model gateway.

## Core Job

When given a RouterBase task:

1. Keep model calls in trusted server side code.
2. Set the OpenAI compatible base URL to `https://routerbase.com/v1`.
3. Read the API key from `ROUTERBASE_API_KEY`.
4. Keep model IDs configurable through environment variables.
5. Preserve the existing request shape where possible.
6. Add clear error handling for auth, model, quota, rate, and timeout failures.
7. Design fallback behavior only when the fallback model has the same output contract.

## Recommended Environment Variables

- `ROUTERBASE_API_KEY`
- `ROUTERBASE_BASE_URL`
- `ROUTERBASE_CHAT_MODEL`
- `ROUTERBASE_CHAT_FALLBACK_MODEL`
- `ROUTERBASE_EMBEDDING_MODEL`
- `ROUTERBASE_IMAGE_MODEL`
- `ROUTERBASE_VIDEO_MODEL`

## TypeScript Example

```ts
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.ROUTERBASE_API_KEY,
  baseURL: process.env.ROUTERBASE_BASE_URL || "https://routerbase.com/v1",
});

export async function summarize(text: string) {
  const result = await client.chat.completions.create({
    model: process.env.ROUTERBASE_CHAT_MODEL || "openai/gpt-5.4-mini",
    messages: [{ role: "user", content: text }],
  });

  return result.choices[0]?.message?.content || "";
}
```

## Fallback Rules

- Do not retry authentication failures.
- Do not retry quota failures unless the user has configured another account or model.
- Retry temporary rate or provider errors with a bounded retry count.
- Fall back only to a model that supports the same output format.
- Report degraded mode clearly when fallback was used.

## Media Generation Rules

For image, video, audio, or speech workflows:

1. Validate the prompt and user permissions before starting work.
2. Create a generation job with the selected model.
3. Store job ID, model ID, user ID, and requested output type.
4. Poll with backoff instead of holding one long request open.
5. Save generated assets to durable storage.
6. Return a stable asset reference to the user.

## Output Format

When you finish a RouterBase integration task, report:

- files changed
- environment variables required
- primary and fallback model IDs
- smoke test performed
- privacy notes for prompts, files, and generated assets
- known limits or assumptions

## Safety

- Never put RouterBase keys in browser or mobile bundles.
- Never include sample looking API keys in public examples.
- Never log secrets, full private prompts, or uploaded private files.
- Keep provider specific behavior inside a small adapter.
