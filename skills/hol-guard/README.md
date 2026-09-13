# HOL Guard

HOL Guard adds pre-tool security checks, approval review, receipts, and package verification to local AI workflows. This ZeroClaw skill teaches the agent to invoke the `hol-guard` CLI directly and to use the companion `plugin-scanner` CLI when a user asks to inspect an agent skill, plugin, or MCP package.

## Install

```bash
zeroclaw skills install hol-guard
```

The skill requests only `shell_exec` because it runs the local HOL Guard and plugin-scanner CLIs. It does not request file-write or network permissions from ZeroClaw.

Install HOL Guard itself when needed:

```bash
pipx install hol-guard
hol-guard status
```

For package verification:

```bash
pipx install plugin-scanner
plugin-scanner verify <path>
```

## Example

Ask ZeroClaw to verify an agent skill before use. The skill will check the relevant CLIs, run the scanner against the skill directory, report any findings, and preserve failures as blockers until they are understood.
