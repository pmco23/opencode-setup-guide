# OpenCode Command Reference

All custom commands available in the global OpenCode config. Run with `/command-name [arguments]`.

---

## repo-* — Codebase Analysis

Commands that analyze the local codebase. Most use repomix + ast-grep in combination, and the export commands use the Repomix CLI for durable files. All run as subtasks (isolated context).

| Command | Arguments | Description |
|---|---|---|
| `/repo-overview` | — | Project structure, purpose, technologies, key entry points |
| `/repo-stats` | — | File count, total tokens, largest files |
| `/repo-pack` | — | Full repomix snapshot for the current analysis run |
| `/repo-pack-slim` | — | Compressed structural repomix snapshot with lower token cost |
| `/repo-export` | `[output-path]` | Write a full repomix XML snapshot to disk |
| `/repo-export-slim` | `[output-path]` | Write a compressed repomix XML snapshot to disk |
| `/repo-search` | `<pattern>` | Text search across all files (strings, comments, config) |
| `/repo-auth` | — | Map all auth/session/JWT/permission code |
| `/repo-routes` | — | Map all HTTP routes and endpoint definitions |
| `/repo-models` | — | Map all database models and schema definitions |
| `/repo-errors` | — | Audit error handling: empty catches, swallowed errors |
| `/repo-review` | — | Full code review: tech debt, debug leftovers, secrets, structural issues |

### When to use repo-pack vs repo-pack-slim

- **`/repo-pack`** — when you want a full in-session repomix snapshot, including file contents
- **`/repo-pack-slim`** — large codebases, or when you mostly need structure/signatures with lower token cost
- Both commands analyze the repo in a subtask and do not guarantee a reusable file on disk
- If you want a reusable file on disk, use `/repo-export` or `/repo-export-slim` instead

### Export commands

- **`/repo-export`** — writes a full XML snapshot to `./repomix-output.xml` by default
- **`/repo-export-slim`** — writes a compressed XML snapshot to `./repomix-output-slim.xml` by default
- Pass `[output-path]` to override the destination, for example `/repo-export artifacts/backend.xml`
- These commands use the Repomix CLI, so the file persists after the current session

### repo-search vs ast-find

Use `/repo-search` for text that has no code structure: error message strings, config keys, comments, natural language in docs. Use `/ast-find` for code symbols (function names, class names, imports).

---

## ast-* — Structural Code Search

Commands powered by ast-grep. Understands code syntax — no false positives from comments or string literals. All run as subtasks.

| Command | Arguments | Description |
|---|---|---|
| `/ast` | `<pattern>` | Raw AST pattern search across the codebase |
| `/ast-find` | `<symbol>` | All definitions, call sites, and imports of a symbol |
| `/ast-lang` | `<lang> <pattern>` | AST search scoped to a specific language |
| `/ast-refactor` | `<pattern>` | Find all sites matching a refactoring target |

### ast-grep pattern syntax

```
$VAR          matches any single node
$$$ARGS       matches zero or more nodes (variadic)
```

Examples:
```
/ast console.log($$$ARGS)
/ast-lang typescript async function $FUNC($$$ARGS)
/ast-refactor useState(false)
```

---

## gh-* — GitHub Code Search

Commands that search public GitHub repositories via grep.app. All run as subtasks.

| Command | Arguments | Description |
|---|---|---|
| `/gh-examples` | `<topic>` | Real-world usage examples from GitHub |
| `/gh-pattern` | `<code pattern>` | How a specific code pattern is used across projects |
| `/gh-fix` | `<error or problem>` | Find how others solved this error on GitHub |
| `/gh-docs` | `<library + topic>` | Official docs (Context7) + real-world usage (GitHub) combined |

### Best for
- Validating an approach before implementing
- Finding workarounds for bugs not covered in official docs
- Seeing what options/parameters people actually use in practice

---

## c7-* — Documentation (Context7)

Commands that query official library documentation via Context7. All run as subtasks.

| Command | Arguments | Description |
|---|---|---|
| `/c7-docs` | `<library>` | Full documentation overview for a library |
| `/c7-how` | `<question>` | How to do something with a library |
| `/c7-api` | `<function> <library>` | API reference: signature, parameters, return values |
| `/c7-fix` | `<error message>` | Find docs that explain and fix an error |
| `/c7-migrate` | `<library> <from> to <to>` | Migration guide between versions |

### Examples

```
/c7-docs drizzle-orm
/c7-how handle file uploads in express
/c7-api useQuery react-query
/c7-fix TypeError: Cannot read properties of undefined
/c7-migrate next.js 13 to 14
```

---

## mem-* — Project Memory

Commands that read/write the persistent memory knowledge graph. Run in the **main context** (not subtasks) so recalled information is immediately available.

| Command | Arguments | Description |
|---|---|---|
| `/mem-save` | `<fact or decision>` | Store a decision, convention, or fact |
| `/mem-recall` | — | Retrieve everything remembered about this project |
| `/mem-clear` | — | Wipe all memory for this project |

### What to save

```
/mem-save we use Zod for all input validation, never manual type checks
/mem-save the API follows REST conventions, all handlers are in src/routes/
/mem-save library X has a bug with Y, workaround is to use Z instead
```

### When to recall

Run `/mem-recall` at the start of a new session on an existing project to restore context without re-reading the codebase.

---

## spec-review — OpenSpec Verification (Skill)

A global **skill** (not a slash command) for verifying implementation after `/opsx:apply`. The agent loads it on-demand via the `skill` tool.

**Source:** [`skills/spec-review/SKILL.md`](../skills/spec-review/SKILL.md) in this repo.

**Install:** Symlink the skill directory into your global skills folder:

```bash
mkdir -p ~/.config/opencode/skills
ln -sfn "$(pwd)/skills/spec-review" ~/.config/opencode/skills/spec-review
```

To trigger it, ask the agent:

```
Load the spec-review skill and review the add-dark-mode change
```

Or for the most recent non-archived change:

```
Load the spec-review skill and review the current change
```

### What it checks

**Pass 1 — Spec compliance:**
Reads `openspec/changes/<name>/tasks.md` and uses ast-grep to verify each task is implemented. Reports done / incomplete / missing.

**Pass 2 — Code quality:**
- repomix scans for: `TODO/FIXME/HACK`, `console.log/debugger`, hardcoded secrets
- ast-grep scans for: empty catch blocks, missing return types on exports

---

## Quick Reference by Scenario

| Scenario | Command |
|---|---|
| Starting work on an unfamiliar codebase | `/repo-overview` |
| Before writing a spec proposal | `/repo-overview`, `/repo-pack-slim` |
| Saving a reusable repo snapshot to disk | `/repo-export` or `/repo-export-slim` |
| Researching a library before implementing | `/gh-docs` or `/c7-how` |
| Hit an error during implementation | `/c7-fix` or `/gh-fix` |
| Looking for real-world examples | `/gh-examples` |
| Finding all usages of a function | `/ast-find` |
| Detecting structural anti-patterns | `/repo-errors`, `/ast` |
| After `/opsx:apply` (OpenSpec) | `spec-review` skill |
| Starting a new session on an existing project | `/mem-recall` |
| Saving a key decision | `/mem-save` |
| Upgrading a dependency | `/c7-migrate` |
