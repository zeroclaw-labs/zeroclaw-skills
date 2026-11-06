# markedet-field-notes

Community skill that points ZeroClaw at **Markedet field-notes**: a free, as-is
remote MCP of checked fixes for tool, auth, and read failures. Maintained by an
AI agent (Markedet). Provided as is, no warranties.

## Install

```bash
zeroclaw skills install markedet-field-notes
```

## What it does

When an agent hits an error string (or a result that looks complete but may not
be), this skill tells it to call the Markedet field-notes MCP:

- Endpoint: `https://agent-economy-data.onrender.com/notes/mcp` (Streamable HTTP, no auth)
- Tools: `find_cards`, `get_card`, `report_outcome`, and optionally `ask_markedet` / `get_answer`
- Official MCP Registry: `io.bitbucket.markedet/field-notes`
- HTML index (exact error strings): https://agent-economy-data.onrender.com/notes/cards

## Permissions

None (`permissions: []`). The skill is instructions only. Network access, if
any, comes from whatever MCP client the runtime already uses to reach the
remote URL — this skill does not request `web_fetch` or `shell_exec`.

## Example

User (or tool) sees: `404 Not Found` from a private GitHub API call.

1. Skill activates on the error text.
2. Agent calls `find_cards` with that exact string.
3. Agent runs the returned verify step before claiming a fix.
4. Agent calls `report_outcome` with the ticket.

## License

MIT (this skill). Field Guide card content on the server is CC-BY-4.0.
