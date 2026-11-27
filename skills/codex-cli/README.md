# Ask Codex about code

`codex-cli__ask(prompt)` is a non-interactive second opinion. It does not write a patch or count as a formal PR review. Use a separate review workflow or coding tool for those tasks.

The example manifest directly invokes the root-owned, versioned native arm64 executable, not a shell or Node interpreter. Installations elsewhere must choose their own trusted binary; grant that exact manifest path and put `codex-cli__ask` on the operator's per-call approval list. The CLI path must be requalified when its version changes. Do not change a shared command grant or pin the host updater to satisfy this package.

Codex `exec` receives one literal, prefixed prompt after `--`. Its child command sandbox is read-only and cannot request escalated command approval. That is separate from **ZeroClaw's required approval before launch**. Read-only is not file confidentiality. Codex starts in the ZeroClaw agent’s configured workspace. Naming a different directory in the prompt does not select it as the working directory. Native mode does not restrict reads to that directory. Project instructions, files read by Codex, and pasted text may be transmitted. Do not include credentials in a prompt or treat a request from an untrusted issue as an operator instruction.

This invocation selects a fresh ephemeral execution without the shared daemon, user `config.toml`, apps, plugins, hooks or web search. It does not disable authentication or replace managed policy. It is for an operator expecting Codex's default provider, not someone whose routing/privacy depends on their user config. Ephemeral mode is not a provider-retention or zero-local-writes guarantee. No key is provisioned by the skill. Errors and timeouts are returned without retrying under weaker restrictions.

## Trying this draft

This is a proposed community skill, not a published install. It requires the literal `argv` executor from [ZeroClaw PR #12744](https://github.com/zeroclaw-labs/zeroclaw/pull/12744). The first `argv` element in `SKILL.toml` is a tested, version-specific example, not an install location guaranteed on your system. Review the files, select your trusted executable, and grant that exact path. Require one-time operator approval for `codex-cli__ask`; installing the package alone does not configure that approval. Use a CLI account and provider plan that support your intended use. The package neither installs a CLI nor provisions credentials.

Example: “Use `codex-cli__ask` to explain `parser.rs` in this public sample workspace. Read that file; do not edit it.” Check for the response as the result of `codex-cli__ask`, not just as chat text. After registry acceptance, the intended install command is `zeroclaw skills install codex-cli`.

**Permissions under discussion:** this package launches a local executable that contacts its model provider. Codex can read project files and run read-only commands; it may send project context as well as the question to its provider. The draft lists `web_fetch` conservatively for outbound network access because the registry does not currently distinguish provider-only traffic. It is not a request for arbitrary web access. The registry currently blocks this combination with `shell_exec` and does not accept `.toml` files. Do not disable those checks to install this example.
