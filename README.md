# ZeroClaw Skills

Official skill registry for [ZeroClaw](https://www.zeroclawlabs.ai) — community-contributed AI agent skills, tools, and workflows.

## Install a skill

```bash
zeroclaw skill install <skill-name>
```

## Browse skills

Visit the [Skills Hub](https://www.zeroclawlabs.ai/skills) to search and discover skills.

## Contributing a skill

1. Fork this repository
2. Create your skill folder: `skills/<your-skill-name>/`
3. Add required files:
   - `manifest.toml` — name, version, author, description, category, tags, permissions
   - `SKILL.md` — instructions/prompt for the agent
   - `README.md` — documentation
4. Add your skill to `registry.json`
5. Open a pull request

### manifest.toml format

```toml
[skill]
name = "my-skill"
version = "0.1.0"
author = "your-github-username"
description = "What your skill does in one sentence."
category = "tools"           # agents, coding, research, writing, chat, security, data, devops, tools
tags = ["Community"]         # Official, Community, Featured, Experimental
permissions = ["file_read"]  # file_read, file_write, shell_exec, web_search, web_fetch, channel_*
```

### Categories

| Category | Description |
|----------|-------------|
| agents | Multi-agent systems, routers, self-improving agents |
| coding | Code generation, review, refactoring |
| research | Web research, knowledge bases, RAG |
| writing | Documentation, content creation |
| chat | Messaging integrations (Telegram, Discord, Slack, etc.) |
| security | Vulnerability scanning, secret detection |
| data | Data analysis, CSV/JSON/SQL processing |
| devops | CI/CD, API testing, infrastructure |
| tools | Git helpers, utilities, general-purpose tools |

## License

MIT
