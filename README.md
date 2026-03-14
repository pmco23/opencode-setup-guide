# OpenCode Setup Guide

Personal reference for setting up [OpenCode](https://opencode.ai) around an [OpenSpec](https://github.com/fission-ai/openspec) workflow.

This repo focuses on the shared OpenCode configuration (MCP servers, custom commands, and review skills) plus the OpenSpec install and command references that plug into a spec-driven workflow.

---

## Shared Prerequisites

- Node.js is required for the shared OpenCode config (`memory`)
- OpenSpec adds the `/opsx:*` command workflow on top of the shared OpenCode setup

---

## Quick Start

1. Create the global OpenCode config directories:
   ```bash
   mkdir -p ~/.config/opencode/skills
   ```
2. Copy `opencode.json` to `~/.config/opencode/opencode.json` (global config)
3. Copy the OpenSpec global `AGENTS.md` preset:
   - `cp agents/openspec.md ~/.config/opencode/AGENTS.md`
4. Install skills (see below)
5. Follow the [OpenSpec install guide](docs/openspec-install.md)

Project `AGENTS.md` remains an optional team-shared layer inside a repo. The presets in `agents/` are personal global defaults for OpenCode.

---

## Skills

Two skills are included. Install them by symlinking into your OpenCode skills directory:

```bash
mkdir -p ~/.config/opencode/skills
ln -sfn "$(pwd)/skills/spec-review" ~/.config/opencode/skills/spec-review
ln -sfn "$(pwd)/skills/code-review" ~/.config/opencode/skills/code-review
```

| Skill | Purpose |
|---|---|
| [spec-review](skills/spec-review/SKILL.md) | Post-implementation review for OpenSpec changes |
| [code-review](skills/code-review/SKILL.md) | Pre-commit code quality audit (no OpenSpec required) |

Symlinks mean `git pull` automatically updates the skills.

See [skill usage](docs/opencode-commands.md#spec-review--openspec-verification-skill) for details.

---

## Config Files

| File | Purpose | Install |
|---|---|---|
| `opencode.json` | Global config: MCP servers + custom commands | Copy to `~/.config/opencode/opencode.json` |
| `agents/openspec.md` | Global AGENTS preset for OpenSpec workflows | Copy to `~/.config/opencode/AGENTS.md` |

---

## Index

| Document | What it covers |
|---|---|
| [opencode-setup.md](docs/opencode-setup.md) | MCP servers, tool design principles, workflow integration |
| [openspec-install.md](docs/openspec-install.md) | Install OpenSpec, enable extended workflow profile, brownfield seeding |
| [opencode-commands.md](docs/opencode-commands.md) | Custom commands (`/c7-*`, `/mem-*`, `/ast-find`) and review skills |
| [openspec-commands.md](docs/openspec-commands.md) | All `/opsx:*` commands |
| [opencode-workflows.md](docs/opencode-workflows.md) | End-to-end workflows combining OpenCode with OpenSpec |
