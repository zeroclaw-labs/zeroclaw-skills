# Ask Claude Code about a trusted project

`claude-cli__ask(prompt)` follows [Anthropic’s programmatic usage guide](https://code.claude.com/docs/en/headless#basic-usage): run `claude -p` and return its text output. ZeroClaw passes `argv = ["claude", "-p", "--", "{{prompt}}"]`. The `--` ends option parsing so a question beginning with a dash is still a positional argument. No shell or extra flags input is needed. Do not put secrets in the prompt; process arguments can be visible locally.

The skill leaves model, effort, tools, permissions and session storage to the installed CLI. It does not auto-approve tools, force a permission mode, choose an account, or promise read-only access. A print-mode session may be recorded locally. Anthropic recommends considering `--bare` for CI, but it skips normal customizations and does not use the Claude subscription OAuth login. This skill is for an operator’s configured CLI, not a clean-room CI runner.

**Only approve a run in a trusted workspace.** Headless Claude Code skips the workspace trust dialog: project hooks can run and MCP servers can connect at startup. The CLI can attach referenced files, use configured tools, read or change files, and send context to its provider. ZeroClaw approval is for the run, not for each downstream action. Name the intended workspace and sharing scope in the prompt; naming a path does not change the working directory or confine access. Use OS isolation if required. This is not a formal code review or a text-only tool.

The skill allows up to one hour per invocation (`timeout_secs = 3600`). This is a wall-clock limit, not an inactivity timer. A shorter caller or channel turn limit can still end the run first. Timing out does not undo completed edits or external actions.

## Trying this draft

This is a proposed community skill, not a published install. It requires the literal `argv` executor from [ZeroClaw PR #12744](https://github.com/zeroclaw-labs/zeroclaw/pull/12744). Install `claude` on the ZeroClaw service’s trusted `PATH` and explicitly list `"claude"` in `allowed_commands`; a wildcard or a path-only grant does not authorize the bare name. Core rejects a launcher if its resolved filename differs from `claude` (for example, a symlink to a differently named script). Require one-time operator approval for `claude-cli__ask`; installing the package alone does not configure that approval. Use a CLI account and provider plan that support your intended use. The package neither installs a CLI nor provisions credentials.

Example: “In the approved parser checkout, why does the empty-input test pass? Cite the implementation and test; do not edit files.” Check the citations against the checkout and check for changes. The result should arrive as the result of `claude-cli__ask`, not just chat text. The no-edit request is not an enforced restriction.

**Permissions under discussion:** this draft declares executable, file read/write and network capabilities. Model requests and configured integrations can send data outside the workspace. The registry currently rejects `.toml` files and `shell_exec` combined with `web_fetch`; do not disable those checks to install this example.
