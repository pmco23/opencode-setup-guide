# OpenCode Setup Guide

Personal reference for the global OpenCode configuration at `~/.config/opencode/opencode.json`.

## Shared prerequisites

- Node.js is required for the shared OpenCode config because `memory` runs locally via `npx`

---

## MCP Servers

Three MCP servers are configured globally. Each serves a distinct purpose.

### context7
**What:** Official library documentation lookup.
**When:** You need accurate, up-to-date API references or usage guides for any library.
```json
{
  "type": "remote",
  "url": "https://mcp.context7.com/mcp"
}
```
> Optional: set `CONTEXT7_API_KEY` env var and add a `headers` field for higher rate limits.

---

### ast-grep
**What:** Structural AST-aware code search. Understands code syntax, not just text.
**When:** You need precise pattern matching — finding function definitions, call sites, imports, or structural anti-patterns without false positives from comments or strings.
```json
{
  "type": "local",
  "command": ["sh", "-c", "docker run -i --rm -v \"$(pwd):/src\" mcp/ast-grep"]
}
```

---

### memory
**What:** Persistent knowledge graph across sessions.
**When:** You want the agent to remember project decisions, conventions, or context between separate chat sessions.
```json
{
  "type": "local",
  "command": ["npx", "-y", "@modelcontextprotocol/server-memory"]
}
```

---

## Tool Design Principles

### context7 — when to use

| Situation | Use |
|---|---|
| Official API reference | context7 |
| How-to guides and tutorials | context7 |
| Migration guides between versions | context7 |

### ast-grep — when to use

| Situation | Use |
|---|---|
| Finding all usages of a specific function/class | ast-grep |
| Detecting structural anti-patterns (empty catches, etc.) | ast-grep |
| Refactoring: find all sites matching a code shape | ast-grep |

### memory — what to store

Good candidates:
- Architecture decisions ("we use Zod for all input validation")
- Conventions ("all API handlers follow the pattern X")
- Known gotchas ("library Y has a bug with Z, workaround is...")
- Project-specific context that would take time to re-derive

---

## OpenSpec Workflow Integration

This setup is designed around [OpenSpec](https://github.com/Fission-AI/OpenSpec): the shared OpenCode config provides documentation lookup, code search, and memory tools that support the spec workflow before and after `/opsx:*` commands.

### Recommended workflow — new feature

```
1. /c7-how              → research implementation approaches before designing
2. /opsx:propose        → create proposal, specs, design, tasks
3. /opsx:apply          → implement
4. spec-review skill    → verify implementation against tasks.md + code audit
5. /opsx:archive        → archive the completed change
```

### Recommended workflow — brownfield feature

```
1. /ast-find <symbol>   → understand existing code patterns
2. /mem-save            → save key findings (patterns, conventions, gotchas)
3. /opsx:propose        → write spec informed by what actually exists
4. /opsx:apply          → implement
5. spec-review skill    → verify + code audit
6. /opsx:archive
```

### Recommended workflow — bug fix

For bugs, skip the full spec pipeline. Diagnose first, then run a minimal spec:

```
1. Investigate          → trace the root cause (explore or plain conversation)
2. /ast-find <symbol>   → find all call sites and definitions involved
```

```
3. /opsx:propose fix-<name> → minimal proposal: what's wrong, what the fix is, tasks
4. /opsx:apply          → fix
5. /opsx:archive
```

Skip `/opsx:verify` for small fixes unless the bug touched shared infrastructure.

For small bug fixes, keep the OpenSpec loop narrow: diagnose first, then run `/opsx:propose fix-<name>`, `/opsx:apply`, and `/opsx:archive`.

### Context hygiene

`mem-*` commands use `subtask: false` intentionally — recalled memory needs to be visible in the active session.

### DCP plugin

The shared config includes `@tarquinen/opencode-dcp@latest`. It adds dynamic context pruning features such as selective compression, duplicate-call cleanup, and `/dcp` inspection commands, which makes long research-heavy sessions easier to manage.

It is an opinionated default for this repo. If that tradeoff is not worth it for you, tune `~/.config/opencode/dcp.jsonc` or remove the plugin from your personal config.

Tradeoffs to know:
- It can reduce prompt-cache efficiency compared with vanilla OpenCode
- Prune notifications can feel noisy in chat depending on your settings
- By default it mainly helps the parent session, not every subtask flow

### AGENTS.md — global rules

OpenCode loads `~/.config/opencode/AGENTS.md` on every session, across all projects. This is where personal defaults live — things that should always apply regardless of what project you're in.

Our global `AGENTS.md` encodes:

- **Session start**: run `/mem-recall` before anything else on an existing project
- **Research**: use `context7` for official docs
- **Codebase exploration**: use `ast-grep` for precise structural queries
- **After implementing**: load the `spec-review` skill before archiving
- **Memory**: save decisions, conventions, and gotchas whenever discovered

```
~/.config/opencode/AGENTS.md   ← personal global rules (not committed to projects)
AGENTS.md                       ← project rules (committed to Git, shared with team)
```

The project `AGENTS.md` is for team-shared conventions. The global one is for your personal workflow defaults. Copy the OpenSpec template from `agents/` in this repo:

```bash
mkdir -p ~/.config/opencode
cp agents/openspec.md ~/.config/opencode/AGENTS.md
```

Use a project `AGENTS.md` separately when you want repo-specific rules committed for the team.

### Project config tips

OpenSpec uses a project-level config file to encode architectural constraints. Fill it in before generating artifacts so specs and tasks reflect your actual architecture.

#### OpenSpec — openspec/config.yaml

```yaml
# openspec/config.yaml
context: |
  This project uses TypeScript with strict mode. All input validation uses Zod.
  API handlers follow the repository pattern — no direct DB access in routes.
  Authentication is JWT-based via the auth middleware in src/middleware/auth.ts.
  All database access goes through src/db/repositories/.
  Tests use Vitest. Every new module needs a corresponding test file.
```

## Configuration File Locations

```
~/.config/opencode/opencode.json      ← global config: MCPs + commands (all projects)
~/.config/opencode/AGENTS.md          ← global rules: personal workflow defaults
~/.config/opencode/skills/<name>/     ← global skills (symlinked from this repo)

.claude/skills/<name>/                ← slash-command skills generated by OpenSpec

.opencode/opencode.json               ← per-project config (overrides global)
AGENTS.md                             ← per-project rules (committed to Git)
.opencode/skills/<name>/              ← per-project skills
```

`~/.config/opencode/skills/` and `.opencode/skills/` are OpenCode skill directories. `.claude/skills/` is a separate mechanism used by OpenSpec to inject slash-command prompts, so these locations can coexist without conflict.

### Installing skills from this repo

The skill source files live in `skills/` in this repo. Symlink them into the global skills directory so OpenCode discovers them:

```bash
mkdir -p ~/.config/opencode/skills
ln -sfn "$(pwd)/skills/spec-review" ~/.config/opencode/skills/spec-review
```

This keeps the repo as the single source of truth — `git pull` updates the skills automatically.
