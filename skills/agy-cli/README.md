# Antigravity CLI: ask about a trusted project

Use `agy-cli__ask(prompt)` for explanations, design comparisons and implementation plans. It starts one AGY print invocation in the agent’s workspace with `--mode=plan` and `--sandbox`. These are AGY settings: plan mode expresses the task intent; `--sandbox` restricts terminal use. Neither makes AGY a text-only model or guarantees a read-only filesystem.

Approve this as a **delegated AGY run**. ZeroClaw checks the executable at launch; it does not individually approve AGY’s own actions. Depending on its configuration, AGY may use file or terminal tools, read or write files, contact integrations such as MCP, or retain local and provider-side state. Native mode runs with the OS user’s access, not just access to the current directory. Don’t use a native run for untrusted inputs, repositories or shared-channel traffic. Use a trusted, non-sensitive checkout whose contents may be sent to the provider. An empty directory reduces incidental context but does not provide isolation.

For the tested installation the exact grant is `/usr/local/libexec/agy-1.5.18`. On other hosts pair a trusted binary with an exact grant. Keep launch approval supervised. The operator may see only the tool name and prompt at approval: establish the workspace and sharing scope first. Authentication stays with the CLI. Bubblewrap is optional; selecting it does not grant credentials or egress. Never fall back silently.

`--print={{prompt}}` passes one argument without a shell; slash commands are disabled. The prompt is subject to OS argument-size limits and may be visible to local process inspection. Don’t include credentials. The CLI print timeout is 90 seconds; the executor timeout is 120 seconds. A timeout is not an undo for file changes, external calls or detached work.

## Trying this draft

This is a proposed community skill, not a published install. It requires the literal `argv` executor from [ZeroClaw PR #12744](https://github.com/zeroclaw-labs/zeroclaw/pull/12744). The first `argv` element in `SKILL.toml` is a tested, version-specific example, not an install location guaranteed on your system. Review the files, select your trusted executable, and grant that exact path. Require one-time operator approval for `agy-cli__ask`; installing the package alone does not configure that approval. Use a CLI account and provider plan that support your intended use. The package neither installs a CLI nor provisions credentials.

Example: “Use `agy-cli__ask` to compare two approaches in the named public checkout. Return a plan; make no edits.” Check for the response as the result of `agy-cli__ask`, not just as chat text. After registry acceptance, the intended install command is `zeroclaw skills install agy-cli`.

**Permissions under discussion:** this package launches a local executable that contacts its model provider. The provider request may include context AGY gathers using its own tools. AGY can run commands and may write files; a plan request is not enforcement. The draft lists `web_fetch` conservatively for outbound network access because the registry does not currently distinguish provider-only traffic. It is not a request for arbitrary web access. The registry currently blocks this combination with `shell_exec` and does not accept `.toml` files. Do not disable those checks to install this example.

For intended headless use, check the [Antigravity CLI FAQ](https://antigravity.google/docs/faq/). A successful test with a signed-in CLI is not evidence of plan eligibility or provider endorsement.
