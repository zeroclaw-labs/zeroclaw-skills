# Orca Replay

Read a recording of a past agent run to answer what actually happened — and re-run that recording offline, without contacting a model provider.

## What it does

An agent asked "why did you do that?" answers from a summary of its own context window. The tool results, the exit codes, and the files that changed without anyone mentioning them are already gone. The answer comes out fluent, confident, and occasionally wrong, which is worse than "I don't know", because it gets believed and written into a commit message.

[OrcaReplay](https://github.com/Continuum-AI-Corp/OrcaReplay) records the run at the model-provider boundary — prompts, tool calls, responses, raw bytes — into a local trace. This skill teaches the agent to read that trace instead of guessing, and to replay it offline when the question is whether the failure reproduces.

## Install

```bash
npm install -g orcareplay     # Node 20+, exposes the `orca` command
orca list                     # confirm at least one recorded run exists
```

The package also exposes an MCP server (`orca mcp`) with read tools over the local trace library.

## Requirements

- Node 20 or later
- The `orca` CLI on PATH (or approval to run it through `npx`)
- At least one recorded run — `orca list` is the check

## Permissions

`shell_exec` to run `orca`, and `file_read` to read the trace output. The skill does not write files and does not request network permissions: replaying is deliberately offline, so it never needs to reach a provider.

## Recording

Recording happens out of process. The agent is launched as a child process with its model-provider origin redirected for that process only, so the program that gets captured is the program as it really ran rather than an instrumented variant.

## Related

- Upstream project: https://github.com/Continuum-AI-Corp/OrcaReplay
- License: Apache-2.0
