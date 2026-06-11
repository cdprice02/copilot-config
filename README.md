# copilot-config

Personal GitHub Copilot CLI configuration — synced across machines via git.

## What's tracked

| Path | Purpose |
|---|---|
| `CLAUDE.md` | Guidance for Claude models (Copilot CLI + Claude Code) |
| `copilot-instructions.md` | General Copilot CLI instructions |
| `copilot-instructions-general.md` | Cross-project standards for all languages |
| `copilot-instructions-python.md` | Python-specific guidelines |
| `copilot-instructions-rust.md` | Rust-specific guidelines |

## What's NOT tracked

Session state, logs, credentials, command history, platform binaries, and `node_modules`.
All are regenerated automatically by Copilot CLI on each machine.

## Setup on a new machine

### 1. Clone

```bash
git clone git@github.com:cdprice02/copilot-config.git ~/.config/copilot
```

Or if the directory already exists:

```bash
cd ~/.config/copilot
git init
git remote add origin git@github.com:cdprice02/copilot-config.git
git fetch
git checkout -b main --track origin/main
```

### 2. Project-specific overrides

Each repo can override or extend these instructions via `.github/copilot-instructions.md`.
Resolution order (highest to lowest priority):

1. `.github/copilot-instructions.md` (project-specific)
2. Language-specific file (`copilot-instructions-rust.md`, etc.)
3. `CLAUDE.md` / `copilot-instructions-general.md`

## Syncing

```bash
cd ~/.config/copilot
git pull          # pull latest
git add -p
git commit
git push
```
