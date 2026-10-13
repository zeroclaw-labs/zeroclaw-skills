# RouterBase Model Gateway

RouterBase Model Gateway helps agents configure [routerbase](https://routerbase.com/) as an OpenAI compatible gateway for model routing, fallback behavior, and media generation workflows.

## Install

```bash
zeroclaw skills install routerbase-model-gateway
```

## What It Does

- Migrates OpenAI compatible clients to the RouterBase base URL.
- Keeps credentials in `ROUTERBASE_API_KEY`.
- Centralizes model IDs for chat, embeddings, image, video, audio, and speech.
- Guides retry and fallback behavior for production model calls.
- Separates long running media generation into job creation, polling, storage, and status reporting.

## Permissions

This skill declares no ZeroClaw runtime permissions:

```yaml
permissions: []
```

It is an integration guide. The user or agent may still edit project files during a real implementation, but the skill itself does not require shell execution, file access, or network access.

## Example Usage

- "Move this OpenAI chat route to RouterBase."
- "Add a fallback model through RouterBase."
- "Set up RouterBase image and video generation with polling."
- "Make model IDs configurable through environment variables."

## Safety Notes

Keep API keys server side. Do not commit real credentials, sample looking keys, private prompts, uploaded files, or generated private assets.
