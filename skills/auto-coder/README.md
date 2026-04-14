# auto-coder

Autonomous code generation agent for ZeroClaw.

## What it does
- Reads project context and understands your codebase
- Generates, reviews, and refactors code
- Runs tests and iterates until they pass
- Follows existing project patterns

## Install
```bash
zeroclaw skills install auto-coder
```

## Permissions
- `file_read` — read source files
- `file_write` — write/edit files
- `shell_exec` — run tests and build commands
