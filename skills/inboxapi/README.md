# inboxapi

Use InboxAPI from ZeroClaw through the official InboxAPI CLI.

```bash
zeroclaw skills install inboxapi
```

## What it does

- Searches mailbox content by sender, subject, and date.
- Reads single messages and full thread context.
- Drafts and sends new outbound mail.
- Replies in-thread through InboxAPI so threading stays intact.
- Forwards mail and downloads attachments when needed.

## Prerequisites

1. Install the InboxAPI CLI.
   - Preferred: `npm install -g @inboxapi/cli`
   - Fallback: if the global binary is missing, stop and ask the user to either install the CLI themselves or explicitly approve `npx -y @inboxapi/cli ...` for the current session.
2. Authenticate once:

```bash
inboxapi login
```

## Permissions

- `shell_exec`
  - Required because the skill operates by invoking the InboxAPI CLI from ZeroClaw's shell tool.

## Example usage

- "Show me the latest 10 InboxAPI emails and summarize anything urgent."
- "Find emails from alice@example.com about the pricing review."
- "Read the thread for this message id and draft a reply."
- "Send a new email to bob@example.com with the status update below."

## Notes

- The skill keeps InboxAPI as the source of truth for reply threading by using `send-reply --message-id`.
- `npx -y @inboxapi/cli` fetches and executes npm package code at runtime, so it must not be used silently as an automatic fallback.
- Outbound sends should still be previewed and explicitly approved before the agent executes them.
