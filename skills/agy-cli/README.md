# Antigravity CLI: ask about a trusted project

Use `agy-cli__ask(prompt)` for explanations, design comparisons and implementation plans. It sends one question using [AGY headless mode](https://antigravity.google/docs/cli/headless/) and returns the CLI’s default text output. The skill does not override your model, reasoning effort or execution mode. If no mode is configured, AGY 1.5.7+ uses auto where available; workspace edits and some commands can then proceed without prompting. A request for an explanation is not a read-only guarantee.

Approve this as a **delegated AGY run**. ZeroClaw checks the executable at launch; it does not individually approve AGY’s own actions. Depending on its configuration, AGY may use file or terminal tools, read or write files, contact integrations such as MCP, or retain local and provider-side state. Native mode runs with the OS user’s access, not just access to the current directory. Don’t use a native run for untrusted inputs, repositories or shared-channel traffic. Use a trusted, non-sensitive checkout whose contents may be sent to the provider. An empty directory reduces incidental context but does not provide isolation.

The manifest runs the installed `agy` CLI (CLI reference checked with 1.5.18). Keep launch approval supervised. The operator may see only the tool name and prompt at approval: establish the workspace and sharing scope first. Authentication stays with the CLI. Bubblewrap is optional; selecting it does not grant credentials or egress. Never fall back silently.

The complete invocation is `argv = ["agy", "-p", "{{prompt}}"]`. ZeroClaw passes the prompt as one argument without a shell. AGY interprets it normally, so a leading slash may invoke a command or expand a skill. Only approve that when intended. The skill does not set AGY’s terminal sandbox; ZeroClaw’s own sandbox and approval policy still apply. Don’t put credentials in the prompt: argv is subject to OS size limits and may be visible to local process inspection.

Set persistent preferences such as the model in AGY itself. This tool accepts a prompt, not arbitrary CLI flags.

The skill allows up to one hour per invocation (`timeout_secs = 3600`). This is a wall-clock limit, not an inactivity timer. A shorter caller or channel turn limit can still end the run first. Timing out does not undo completed edits or external actions. AGY may still return an answer after a tool action was denied; verify cited evidence when the answer depends on reading a file.

## Trying this draft

This is a proposed community skill, not a published install. It requires the literal `argv` executor from [ZeroClaw PR #12744](https://github.com/zeroclaw-labs/zeroclaw/pull/12744). Install `agy` on the ZeroClaw service’s trusted `PATH` and explicitly list `"agy"` in `allowed_commands`; a wildcard or a path-only grant does not authorize the bare name. Core rejects a launcher if its resolved filename differs from `agy` (for example, a symlink to a differently named script). Require one-time operator approval for `agy-cli__ask`; installing the package alone does not configure that approval. Use a CLI account and provider plan that support your intended use. The package neither installs a CLI nor provisions credentials.

Example: “Use `agy-cli__ask` to compare two approaches in the named public checkout. Return a plan; make no edits.” Check for the response as the result of `agy-cli__ask`, not just as chat text. After registry acceptance, the intended install command is `zeroclaw skills install agy-cli`.

**Permissions under discussion:** this package launches a local executable that contacts its model provider. The provider request may include context AGY gathers using its own tools. AGY can run commands and may write files; a plan request is not enforcement. The draft lists `web_fetch` conservatively for outbound network access because the registry does not currently distinguish provider-only traffic. It is not a request for arbitrary web access. The registry currently blocks this combination with `shell_exec` and does not accept `.toml` files. Do not disable those checks to install this example.

For intended headless use, check the [Antigravity CLI FAQ](https://antigravity.google/docs/faq/). A successful test with a signed-in CLI is not evidence of plan eligibility or provider endorsement.
