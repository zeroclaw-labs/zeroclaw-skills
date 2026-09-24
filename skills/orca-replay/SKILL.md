---
name: orca-replay
description: >-
  Answer questions about what a past agent run actually did by reading a local
  recording of that run instead of the agent's own summary, and re-run the
  recording offline without contacting any model provider. Use when asked why
  an earlier run deleted or changed a file, which step broke a build, whether a
  reported failure still reproduces, or how a different model would have handled
  the same task.
version: "0.1.0"
author: xizhuomengcontin
license: Apache-2.0
category: tools
tags:
  - Community
  - forensics
  - replay
permissions:
  - shell_exec
  - file_read
---

# Orca Replay

You answer questions about runs that already happened. A recording is evidence; the agent's recollection of its own session is not, and neither is a transcript — both are missing the tool results, the exit codes, and the files that changed without anyone mentioning them.

The rule: **when a question is about something that already happened, read the recording before answering.** Do not reconstruct it. If a recording exists, guessing is the wrong move even when the guess would have been right.

The tool is [OrcaReplay](https://github.com/Continuum-AI-Corp/OrcaReplay) — Apache-2.0, Node 20+, published on npm as `orcareplay`, exposing the `orca` command. It sits between the agent and the model provider and writes the real traffic (prompts, tool calls, responses, raw bytes) to a local trace.

## Why this belongs next to a skill

This registry's own README is explicit that runtime behavior — network calls, package installs, files written, commands run — is the user's responsibility to evaluate, and that CI structure checks are not a substitute for that. A recording is how you check after the fact: what the run sent, what came back, and which shell command produced which file change. A replay then re-runs it against the recording, with no provider contacted, so you can look again without paying for the calls twice.

## Preflight

1. Confirm the CLI is present.
   - `orca --version`
   - If `orca` is not on PATH, stop and tell the user to install it: `npm install -g orcareplay`, or ask for explicit approval before running `npx -y orcareplay`.
2. Confirm there is something to read: `orca list`. If it is empty, say so plainly and offer to start a recording. Do not fall back to memory or to a pasted transcript.
3. Establish which run is meant. Every command defaults to the most recent run, so say out loud which run you are reading before you read it.

## Reading a run

- **What happened** — `orca show` gives the timeline: model turns with token counts and stop reasons, tool calls with arguments and results, shell commands with exit codes, every file changed. Good for orientation, long for one specific question.
- **Why it happened** — `orca graph --to <event-seq>` returns only the causal chain that produced that one event. Prefer this for "why" questions; reading a 200-event timeline and reasoning over it invites exactly the confident guess this workflow exists to prevent.

Every edge carries a label:

- **recorded** — the recorder watched it happen and wrote it into the trace.
- **inferred** — derived at query time from a rule the edge names. The trace does not vouch for it.

Keep the two apart in what you tell the user. "The trace shows the `rm` at step 14 removed it" and "this looks like the `rm` at step 14, going by timing — that edge is inferred" are different claims. Name the rule whenever an inferred edge carries the conclusion.

## Replaying

A replay is not a dry run. The agent process runs again, so the commands the run issued run again.

1. List the recorded shell commands and tell the user what will repeat. Get agreement before proceeding when anything could write outside the project.
2. Replay into a scratch worktree, not the working tree, so an interrupted replay cannot leave the recorded file tree restored over uncommitted work.
3. Quote the `reused=n/m` line verbatim, then state what remains unknown. A replay proves the recording is self-consistent — not that a fresh run would fail again.

Do not read `reused=3/5` as a partial failure. Harnesses make calls for themselves, such as a quota probe or a session-naming request, and a replay does not repeat them.

## Reporting

| Seq | Event | Source | Observed or inferred | Rule (if inferred) |
|---|---|---|---|---|
| | | | | |

Close with the verdict line, then the residual uncertainty, then what you did not check.

## Boundaries

- No recording, no answer. An empty trace means nothing was captured, usually an agent that pins its own provider origin and ignores base-URL configuration — not that nothing happened.
- Do not edit the skill registry or the recording itself while investigating; work from a copy when a file needs to be preserved.
