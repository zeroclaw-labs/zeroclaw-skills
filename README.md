# ZeroClaw Skills

Official skill registry for [ZeroClaw](https://www.zeroclawlabs.ai) — community-contributed AI agent skills, tools, and workflows.

## Install a skill

```bash
zeroclaw skill install <skill-name>
```

## Browse skills

Visit the [Skills Hub](https://www.zeroclawlabs.ai/skills) to search and discover skills.

---

## Contributing a skill

Anyone can publish a skill. Fork this repo, add your skill, and open a pull request. Two automated CI checks (structure validation + security scanning) must pass before a maintainer reviews.

### Step 1 — Fork & clone

```bash
gh repo fork zeroclaw-labs/zeroclaw-skills --clone
cd zeroclaw-skills
```

### Step 2 — Create your skill folder

```
skills/my-skill/
├── manifest.toml   # Required — metadata
├── SKILL.md        # Required — agent instructions/prompt
└── README.md       # Required — documentation for users
```

Your folder name **must** match the `name` field in `manifest.toml`.

### Step 3 — Write `manifest.toml`

```toml
[skill]
name = "my-skill"
version = "0.1.0"
author = "your-github-username"
description = "What your skill does in one sentence."
category = "tools"
tags = ["Community"]
license = "MIT"
permissions = ["file_read"]
```

**Required fields:** `name`, `version`, `author`, `description`, `category`, `license`

#### Categories

| Category | Description |
|----------|-------------|
| `agents` | Multi-agent systems, routers, self-improving agents |
| `coding` | Code generation, review, refactoring |
| `research` | Web research, knowledge bases, RAG |
| `writing` | Documentation, content creation |
| `chat` | Messaging integrations (Telegram, Discord, Slack, etc.) |
| `security` | Vulnerability scanning, secret detection |
| `data` | Data analysis, CSV/JSON/SQL processing |
| `devops` | CI/CD, API testing, infrastructure |
| `tools` | Git helpers, utilities, general-purpose tools |

#### Accepted licenses (SPDX identifiers)

`MIT` · `Apache-2.0` · `CC-BY-4.0` · `CC-BY-SA-4.0` · `CC0-1.0` · `BSD-2-Clause` · `BSD-3-Clause` · `ISC` · `Unlicense`

#### Permissions

Only request what your skill actually needs. Dangerous combinations are blocked by CI.

| Permission | What it grants |
|------------|---------------|
| `file_read` | Read files on the user's machine |
| `file_write` | Write/edit files |
| `shell_exec` | Run shell commands |
| `web_search` | Search the web |
| `web_fetch` | Fetch URLs |
| `channel_*` | Messaging integrations (`channel_slack`, `channel_discord`, `channel_telegram`, etc.) |

> **Blocked combos:** `shell_exec` + any network permission, `file_write` + `shell_exec`. These combinations allow arbitrary code execution with data exfiltration and will be rejected automatically.

### Step 4 — Write `SKILL.md`

This is the prompt/instructions that ZeroClaw feeds to the AI agent when your skill is activated. Write clear, specific instructions.

```markdown
# My Skill

You are a [role]. When given a task:

1. First step
2. Second step
3. Third step

## Rules
- Be specific about constraints
- Define the output format
```

### Step 5 — Write `README.md`

Documentation for users browsing the registry. Include:

- What the skill does
- Install command (`zeroclaw skill install my-skill`)
- What permissions it needs and why
- Example usage

### Step 6 — Add to `registry.json`

Add an entry to the `skills` array in `registry.json`:

```json
{
  "name": "my-skill",
  "version": "0.1.0",
  "author": "your-github-username",
  "description": "What your skill does in one sentence.",
  "category": "tools",
  "tags": ["Community"],
  "downloads": 0,
  "stars": 0
}
```

### Step 7 — Open a pull request

```bash
git checkout -b add-my-skill
git add skills/my-skill/ registry.json
git commit -m "feat: add my-skill"
git push origin add-my-skill
gh pr create --title "Add my-skill" --body "Adds the my-skill skill for [brief purpose]."
```

---

## What happens when you open a PR

Two CI workflows run automatically:

### Validation (`validate.yml`)
- `registry.json` is valid JSON
- Every skill folder has `manifest.toml`, `SKILL.md`, and a non-empty `SKILL.md`
- All required manifest fields are present
- `license` is a valid SPDX identifier
- Folder names match manifest `name` fields
- `registry.json` and `skills/` folders are in sync

### Security scan (`security-scan.yml`)
- **Secret detection** — scans for leaked API keys, tokens, and passwords
- **File policy** — only `.toml`, `.md`, `.json`, `.wasm` files allowed; max 500KB; no symlinks or hidden files
- **Content scanning** — checks for prompt injection patterns, dangerous shell commands, suspicious URLs, credential leaks, and obfuscated payloads
- **WASM analysis** — blocks dangerous system imports in `.wasm` binaries
- **Registry integrity** — validates semver, categories, no path traversal, no external URLs
- **Permission audit** — flags dangerous permission combinations (see [Permissions](#permissions) above)
- **URL safety** — checks URLs against Google Safe Browsing (when configured)

Both checks must pass. If a check fails, a comment will explain what went wrong.

---

## License

MIT
