# Docs

Personal reference for the OpenCode + OpenSpec setup.

## Config files

Copy these to their respective locations to replicate the setup:

| File | Copy to |
|---|---|
| [`opencode.json`](../opencode.json) | `~/.config/opencode/opencode.json` |
| [`AGENTS.md`](../AGENTS.md) | `~/.config/opencode/AGENTS.md` |

The `spec-review` skill lives at `~/.config/opencode/skills/spec-review/SKILL.md` — see [opencode-commands.md](./opencode-commands.md#spec-review--openspec-verification-skill) for the content.

---

## Index

| # | File | Purpose |
|---|---|---|
| 1 | [opencode-setup.md](./opencode-setup.md) | Install and configure OpenCode globally: MCP servers, tool design principles, OpenSpec integration |
| 2 | [openspec-install.md](./openspec-install.md) | Install OpenSpec, enable the extended workflow profile, first-run guide for new and brownfield projects |
| 3 | [opencode-commands.md](./opencode-commands.md) | Full reference for all custom commands (`repo-*`, `ast-*`, `gh-*`, `c7-*`, `mem-*`, `spec-review`) |
| 4 | [openspec-commands.md](./openspec-commands.md) | Full reference for all `/opsx:*` commands and when to use a lighter workflow |
| 5 | [opencode-workflows.md](./opencode-workflows.md) | End-to-end workflow examples combining OpenCode commands and OpenSpec for common scenarios |
