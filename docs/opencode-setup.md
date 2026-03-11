# OpenCode Setup Guide

Personal reference for the global OpenCode configuration at `~/.config/opencode/opencode.json`.

---

## MCP Servers

Six MCP servers are configured globally. Each serves a distinct purpose.

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

### gh_grep
**What:** Grep over millions of public GitHub repositories.
**When:** You want to see how real production codebases use a library, pattern, or API — not just the docs.
```json
{
  "type": "remote",
  "url": "https://mcp.grep.app"
}
```

---

### repomix
**What:** Packs an entire local codebase into a single consolidated output for analysis.
**When:** You need a high-level overview, want to search across all files at once, or need to analyze a project holistically.
```json
{
  "type": "local",
  "command": ["sh", "-c", "docker run -i --rm -v \"$(pwd):$(pwd)\" -w \"$(pwd)\" ghcr.io/yamadashy/repomix --mcp"]
}
```
> Requires Docker. The `-v` and `-w` flags mount your current working directory into the container at the same path, so `pack_codebase` can read local project files. Without this mount, only remote repository operations work.

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
> Requires Docker.

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

### sequential-thinking
**What:** Forces the model to reason step-by-step before acting.
**When:** Complex multi-step tasks (implementing features, refactoring, debugging) where rushing leads to mistakes. Works best combined with OpenSpec workflows.
```json
{
  "type": "local",
  "command": ["npx", "-y", "@modelcontextprotocol/server-sequential-thinking"]
}
```

---

## Tool Design Principles

### repomix vs ast-grep — when to use which

| Situation | Use |
|---|---|
| Exploring an unfamiliar codebase | repomix |
| Finding files that contain a topic | repomix |
| Searching strings, comments, config values | repomix |
| Finding all usages of a specific function/class | ast-grep |
| Detecting structural anti-patterns (empty catches, etc.) | ast-grep |
| Refactoring: find all sites matching a code shape | ast-grep |
| Hybrid: discover files then extract structure | repomix → ast-grep |

The `repo-auth`, `repo-routes`, `repo-models` commands use both in sequence: repomix to discover which files are relevant, ast-grep to extract precise structural details from those files.

### context7 vs gh_grep

| Situation | Use |
|---|---|
| Official API reference | context7 |
| How-to guides and tutorials | context7 |
| Migration guides between versions | context7 |
| How the community actually uses something | gh_grep |
| Debugging: find real solutions to an error | gh_grep |
| Validate if docs match real-world usage | both (`/gh-docs`) |

### memory — what to store

Good candidates:
- Architecture decisions ("we use Zod for all input validation")
- Conventions ("all API handlers follow the pattern X")
- Known gotchas ("library Y has a bug with Z, workaround is...")
- Project-specific context that would take time to re-derive

---

## OpenSpec Integration

This setup is designed to work alongside [OpenSpec](https://github.com/Fission-AI/OpenSpec) (spec-driven development). See [openspec-install.md](./openspec-install.md) for the installation guide and [openspec-commands.md](./openspec-commands.md) for the full command reference.

### Recommended workflow — new feature

```
1. /repo-overview          → understand the codebase before writing specs
2. /opsx:propose           → create proposal, specs, design, tasks
3. /c7-how or /gh-docs     → research before implementing
4. /opsx:apply             → implement (clear context first)
5. spec-review skill      → verify implementation against tasks.md + code audit
6. /opsx:archive           → archive the completed change
```

### Recommended workflow — brownfield feature

OpenSpec has no built-in codebase ingestion step. Our `repo-*` commands fill that gap before the spec phase:

```
1. /repo-overview          → map existing structure, tech stack, entry points
2. /repo-auth              → understand existing auth before touching it
3. /repo-routes            → map existing endpoints to avoid collisions
4. /repo-models            → understand the data model before changing it
5. /mem-save               → save key findings (patterns, conventions, gotchas)
6. /opsx:propose           → write spec informed by what actually exists
7. /opsx:apply             → implement
8. spec-review skill      → verify + code audit
9. /opsx:archive
```

See [Workflow 5](./opencode-workflows.md#5-adding-a-feature-to-an-existing-codebase-brownfield) for the full step-by-step including `config.yaml` seeding.

### Recommended workflow — bug fix

For bugs, skip the full spec pipeline. Use explore to diagnose, then a minimal propose:

```
1. /opsx:explore           → investigate the bug, trace the root cause
2. /ast-find <symbol>      → find all call sites and definitions involved
3. /repo-errors            → check if related error handling issues exist nearby
4. /opsx:propose fix-<name> → minimal proposal: what's wrong, what the fix is, tasks
5. /opsx:apply             → fix
6. /opsx:archive
```

Skip `/opsx:verify` for small fixes unless the bug touched shared infrastructure.

See [Workflow 6](./opencode-workflows.md#6-bug-fix-with-openspec) for the full step-by-step.

### Context hygiene

OpenSpec recommends a clean context window before `/opsx:apply`. The heavy MCP tools (repomix, ast-grep) add significant tokens. Since all `repo-*` commands use `subtask: true`, they run in isolated contexts and don't pollute your implementation session.

`mem-*` commands use `subtask: false` intentionally — recalled memory needs to be visible in the active session.

### AGENTS.md — global rules

OpenCode loads `~/.config/opencode/AGENTS.md` on every session, across all projects. This is where personal defaults live — things that should always apply regardless of what project you're in.

Our global `AGENTS.md` encodes:

- **Session start**: run `/mem-recall` before anything else on an existing project
- **Multi-step tasks**: always use `sequential-thinking` before acting on anything with more than 2 steps
- **Research**: use `context7` for official docs, `gh_grep` for real-world patterns
- **Codebase exploration**: use `repomix` for broad discovery, `ast-grep` for precise queries
- **OpenSpec**: always run `repo-*` commands and seed `config.yaml` before `/opsx:propose` on brownfield; use lightweight pipeline for bugs
- **After `/opsx:apply`**: load the `spec-review` skill before archiving
- **Memory**: save decisions, conventions, and gotchas whenever discovered

```
~/.config/opencode/AGENTS.md   ← personal global rules (not committed to projects)
AGENTS.md                       ← project rules (committed to Git, shared with team)
```

The project `AGENTS.md` is for team-shared conventions. The global one is for your personal workflow defaults.

### AGENTS.md — project tip

Add this to a project's `AGENTS.md` to reinforce the sequential thinking habit at the project level:

```md
When implementing a feature, refactoring, or solving a bug with more than
2 steps, use the sequential-thinking tool to plan before acting.

When you need to search docs, use context7 tools.
```

### openspec/config.yaml tip

Use the project config to encode architectural constraints that OpenSpec should respect in every proposal and design it generates. This fills the role that Spec-Kit's `constitution.md` plays:

```yaml
# openspec/config.yaml
context: |
  This project uses TypeScript with strict mode. All input validation uses Zod.
  API handlers follow the repository pattern — no direct DB access in routes.
  Authentication is JWT-based via the auth middleware in src/middleware/auth.ts.
  All database access goes through src/db/repositories/.
  Tests use Vitest. Every new module needs a corresponding test file.
```

The agent reads this before generating any artifact, so the design and tasks will reflect your actual architecture.

---

## Configuration File Locations

```
~/.config/opencode/opencode.json      ← global config: MCPs + commands (all projects)
~/.config/opencode/AGENTS.md          ← global rules: personal workflow defaults
~/.config/opencode/skills/<name>/     ← global skills (e.g. spec-review)

.opencode/opencode.json               ← per-project config (overrides global)
AGENTS.md                             ← per-project rules (committed to Git)
.opencode/skills/<name>/              ← per-project skills
```
