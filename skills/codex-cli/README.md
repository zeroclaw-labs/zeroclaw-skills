# Run Codex non-interactively

`codex-cli__ask(prompt)` follows [OpenAI’s non-interactive guide](https://learn.chatgpt.com/docs/non-interactive-mode): `codex exec` takes a task and prints the final answer to stdout. The skill uses `argv = ["codex", "exec", "--", "{{prompt}}"]`. `--` ends option parsing; the prompt is passed as one argument, without a shell or added instructions. Supply an actual question, not the single `-` that Codex treats as its stdin sentinel. Do not put secrets in process arguments.

**Working directory:** Start in a trusted Git checkout. ZeroClaw starts Codex in the agent’s configured workspace, not a directory named in the prompt. The skill does not skip the repository check or save trust. If Codex rejects the directory, report that error rather than broadening access.

**What approval covers:** The skill does not select a sandbox, model, approval policy, provider, session-storage mode or integration set. OpenAI documents a read-only default for `exec`, but this is not a read-only guarantee from the skill. Depending on local settings, Codex can use tools, change files, retain sessions and send project context outside the machine. In trusted projects, configured MCP servers can run commands outside Codex’s command sandbox. Use only a trusted workspace and configuration. A grant to launch the CLI is not permission to share arbitrary data.

The current executor waits synchronously. The local draft uses `timeout_secs = 3600` as a wall-clock limit; it is not an idle timer, a per-call setting or a guarantee the calling channel will wait that long. Timing out does not undo completed actions.

## Trying this draft

This is a proposed community skill, not a published install. It requires the literal `argv` executor from [ZeroClaw PR #12744](https://github.com/zeroclaw-labs/zeroclaw/pull/12744). Install `codex` on the ZeroClaw service’s trusted `PATH` and explicitly list `"codex"` in `allowed_commands`; a wildcard or a path-only grant does not authorize the bare name. Core rejects a launcher if its resolved filename differs from `codex` (for example, a symlink to a differently named script). Require one-time operator approval for `codex-cli__ask`; installing the package alone does not configure that approval. Use a CLI account and provider plan that support your intended use. The package neither installs a CLI nor provisions credentials.

Example: “In this approved checkout, explain why the parser's empty-input test passes. Cite the implementation and test; do not edit files.” Verify the citations and check that the tree was not changed. Look for the answer as the result of `codex-cli__ask`, not just as chat text.

**Permissions under discussion:** this draft declares executable, file read/write and network capabilities. The registry currently rejects `.toml` files and `shell_exec` combined with `web_fetch`; this example does not change those checks.
