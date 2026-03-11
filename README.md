# OpenCode Setup Guide

Personal reference for setting up [OpenCode](https://opencode.ai) with spec-driven development tools.

This repo covers the shared OpenCode configuration (MCP servers, custom commands, skills) and integrations with two spec-driven workflow tools:

| Tool | What it is | Install guide | Command reference |
|---|---|---|---|
| [OpenSpec](https://github.com/fission-ai/openspec) | Spec-driven dev via `/opsx:*` commands (Node.js) | [openspec-install.md](docs/openspec-install.md) | [openspec-commands.md](docs/openspec-commands.md) |
| [Spec-Kit](https://github.com/github/spec-kit) | Spec-driven dev via `/speckit.*` commands (Python) | [speckit-install.md](docs/speckit-install.md) | [speckit-commands.md](docs/speckit-commands.md) |

Both tools follow the same philosophy: explore → specify → plan → implement → review. The shared OpenCode config works with either one.

---

## Shared Prerequisites

- Node.js is required for the shared OpenCode config (`repomix`, `memory`, `sequential-thinking`)
- Docker is optional and only needed for shared `/ast-*` commands
- Python + `uv` are only needed if you are using Spec-Kit

---

## Quick Start

1. Create the global OpenCode config directories:
   ```bash
   mkdir -p ~/.config/opencode/skills
   ```
2. Copy `opencode.json` to `~/.config/opencode/opencode.json` (global config)
3. Copy the matching global `AGENTS.md` preset:
   - OpenSpec users: `cp agents/openspec.md ~/.config/opencode/AGENTS.md`
   - Spec-Kit users: `cp agents/speckit.md ~/.config/opencode/AGENTS.md`
4. Install skills (see below)
5. Follow the install guide for your spec tool ([OpenSpec](docs/openspec-install.md) or [Spec-Kit](docs/speckit-install.md))

Project `AGENTS.md` remains an optional team-shared layer inside a repo. The presets in `agents/` are personal global defaults for OpenCode.

---

## Skills

Two review skills are included. Install them by symlinking into your OpenCode skills directory:

```bash
mkdir -p ~/.config/opencode/skills

# OpenSpec users
ln -sfn "$(pwd)/skills/spec-review" ~/.config/opencode/skills/spec-review

# Spec-Kit users
ln -sfn "$(pwd)/skills/spec-review-sk" ~/.config/opencode/skills/spec-review-sk
```

| Skill | Purpose |
|---|---|
| [spec-review](skills/spec-review/SKILL.md) | Post-implementation review for OpenSpec changes |
| [spec-review-sk](skills/spec-review-sk/SKILL.md) | Post-implementation review for Spec-Kit specs |

Symlinks mean `git pull` automatically updates the skills.

See [spec-review usage](docs/opencode-commands.md#spec-review--openspec-verification-skill) and [spec-review-sk usage](docs/opencode-commands.md#spec-review-sk--spec-kit-verification-skill) for details.

---

## Config Files

| File | Purpose | Install |
|---|---|---|
| `opencode.json` | Global config: MCP servers + custom commands | Copy to `~/.config/opencode/opencode.json` |
| `agents/openspec.md` | Global AGENTS preset for OpenSpec workflows | Copy to `~/.config/opencode/AGENTS.md` |
| `agents/speckit.md` | Global AGENTS preset for Spec-Kit workflows | Copy to `~/.config/opencode/AGENTS.md` |

---

## Index

| Document | What it covers |
|---|---|
| [opencode-setup.md](docs/opencode-setup.md) | MCP servers, tool design principles, workflow integration |
| [openspec-install.md](docs/openspec-install.md) | Install OpenSpec, enable extended workflow profile, brownfield seeding |
| [speckit-install.md](docs/speckit-install.md) | Install Spec-Kit, initialize in a project, write constitution |
| [opencode-commands.md](docs/opencode-commands.md) | All custom `/repo-*`, `/ast-*`, `/gh-*`, `/c7-*`, `/mem-*` commands and review skills |
| [openspec-commands.md](docs/openspec-commands.md) | All `/opsx:*` commands |
| [speckit-commands.md](docs/speckit-commands.md) | All `/speckit.*` commands |
| [opencode-workflows.md](docs/opencode-workflows.md) | 14 end-to-end workflows combining OpenCode with OpenSpec or Spec-Kit |
