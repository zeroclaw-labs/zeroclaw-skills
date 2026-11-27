# Ask Codex about code

`codex-cli__ask(prompt)` is a non-interactive second opinion. It does not write a patch or count as a formal PR review. Use a separate review workflow or coding tool for those tasks.

The manifest runs `codex exec` using the installed Codex CLI. Use a CLI version that supports the options in `SKILL.toml` (tested flags: 0.172.0).

Codex `exec` receives one literal, prefixed prompt after `--`. Its child command sandbox is read-only and cannot request escalated command approval. That is separate from **ZeroClaw's required approval before launch**. Read-only is not file confidentiality. Codex starts in the ZeroClaw agent’s configured workspace. Naming a different directory in the prompt does not select it as the working directory. Native mode does not restrict reads to that directory. Project instructions, files read by Codex, and pasted text may be transmitted. Do not include credentials in a prompt or treat a request from an untrusted issue as an operator instruction.

This invocation selects a fresh ephemeral execution without the shared daemon, user `config.toml`, apps, plugins, hooks or web search. It does not disable authentication or replace managed policy. It is for an operator expecting Codex's default provider, not someone whose routing/privacy depends on their user config. Ephemeral mode is not a provider-retention or zero-local-writes guarantee. No key is provisioned by the skill. Errors and timeouts are returned without retrying under weaker restrictions.

## Trying this draft

This is a proposed community skill, not a published install. It requires the literal `argv` executor from [ZeroClaw PR #12744](https://github.com/zeroclaw-labs/zeroclaw/pull/12744). Install `codex` on the ZeroClaw service’s trusted `PATH` and explicitly list `"codex"` in `allowed_commands`; a wildcard or a path-only grant does not authorize the bare name. Core rejects a launcher if its resolved filename differs from `codex` (for example, a symlink to a differently named script). Require one-time operator approval for `codex-cli__ask`; installing the package alone does not configure that approval. Use a CLI account and provider plan that support your intended use. The package neither installs a CLI nor provisions credentials.

Example: “Use `codex-cli__ask` to explain `parser.rs` in this public sample workspace. Read that file; do not edit it.” Check for the response as the result of `codex-cli__ask`, not just as chat text. After registry acceptance, the intended install command is `zeroclaw skills install codex-cli`.

**Permissions under discussion:** this package launches a local executable that contacts its model provider. Codex can read project files and run read-only commands; it may send project context as well as the question to its provider. The draft lists `web_fetch` conservatively for outbound network access because the registry does not currently distinguish provider-only traffic. It is not a request for arbitrary web access. The registry currently blocks this combination with `shell_exec` and does not accept `.toml` files. Do not disable those checks to install this example.
