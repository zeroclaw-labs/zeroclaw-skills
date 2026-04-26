# model-evaluator

Production LLM quality monitoring for ZeroClaw. Score outputs, detect drift, get alerts when your AI degrades.

## What it does

Every LLM deployed in production drifts over time — model updates, input distribution shifts, prompt rot. **model-evaluator** watches your LLM outputs continuously and tells you when quality drops, before your users do.

- **Hybrid evaluation** — Rule-based checks (format, length, repetition, refusals) run instantly for free. LLM-as-judge handles nuanced criteria (accuracy, tone, relevance).
- **Drift detection** — Tracks scores over time and alerts when any quality dimension drops below a statistical threshold (1.5σ from baseline).
- **Multi-agent monitoring** — Configure different quality criteria for each of your deployed agents.
- **Alerts anywhere** — Quality alerts go to Slack, Telegram, Discord, email, or wherever you use ZeroClaw.

Think of it as **Datadog for your LLM outputs.**

## Install

```bash
zeroclaw skills install model-evaluator
```

## Permissions

| Permission | Why |
|-----------|-----|
| `file_read` | Read evaluation configs and historical score logs |
| `file_write` | Write evaluation logs and agent configs |
| `web_fetch` | Fetch LLM outputs from API endpoints for evaluation (optional) |

## Usage

### Set up monitoring

```
You: Set up monitoring for my support bot. It's a customer support chatbot
     that answers product questions. Alert me on Slack if quality drops below 4.0.

ZeroClaw: Created monitor config for "support-bot". Tracking: accuracy, relevance,
          completeness, tone, safety. Alert threshold: 4.0. Alerts → Slack.
```

### Evaluate an output

```
You: Evaluate this output from my support bot:
     Input: "How do I reset my password?"
     Output: "To reset your password, click the 'Forgot Password' link on the
              login page. You'll receive a reset email within 2 minutes."

ZeroClaw: ✅ support-bot — Score: 4.6/5
          accuracy: 5 | relevance: 5 | completeness: 4 | tone: 5 | safety: 4
          No alerts triggered. Logged to evaluation history.
```

### Check for drift

```
You: How is my support bot doing this week?

ZeroClaw: 📊 support-bot — 47 evaluations this week
          Overall: 4.2/5 (baseline: 4.4) — Stable
          ⚠️ Tone drifting: 4.5 → 3.8 (-0.7, exceeds 1.5σ)
          Recommendation: Review recent tone scores — responses may be getting
          more terse. Check if the prompt or model was recently updated.
```

### Get a daily report

```
You: Daily report for support-bot

ZeroClaw: 📋 Daily Report — support-bot — Apr 25, 2026
          Evaluations: 12 | Avg: 4.3/5 | Alerts: 1
          Most common issue: completeness (2 low scores)
          Trend: → Stable vs. yesterday
```

## How evaluation works

**Layer 1 — Rule-based checks** run first (free, instant):
- Format compliance (valid JSON/Markdown/etc.)
- Length within expected bounds
- Required fields present
- Language match
- Refusal detection
- Repetition/degeneration detection

**Layer 2 — LLM-as-judge** runs for semantic criteria:
- Accuracy, relevance, completeness, tone, safety
- Plus any custom criteria you define

Scores are combined, logged, and compared against your baseline for drift detection.

## File structure

```
~/.zeroclaw/model-evaluator/
├── configs/          # One JSON file per monitored agent
│   ├── support-bot.json
│   └── summarizer.json
└── logs/             # JSONL evaluation history per agent
    ├── support-bot.jsonl
    └── summarizer.jsonl
```

## Relationship to self-improving-agent

These two skills are complementary, not overlapping:

| | model-evaluator | self-improving-agent |
|---|---|---|
| **When** | Runtime (production) | Dev-time (iteration) |
| **Purpose** | Detect quality problems | Fix prompt problems |
| **Action** | Alert the user | Propose prompt changes |
| **Modifies prompts?** | Never | Yes, that's its job |

A typical workflow: **model-evaluator** detects drift → alerts you → you activate **self-improving-agent** to diagnose and fix the prompt → **model-evaluator** confirms the fix worked.

## License

MIT
