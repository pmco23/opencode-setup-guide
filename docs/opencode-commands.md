# OpenCode Command and Skill Reference

This reference covers the retained OpenCode slash commands plus the `spec-review` global skill. Run slash commands as `/command-name [arguments]`.

---

## Quick Reference by Scenario

| Scenario | Command |
|---|---|
| Looking up a library's official docs | `/c7-docs` |
| Asking how to do something with a library | `/c7-how` |
| Checking a specific API signature or return shape | `/c7-api` |
| Finding all usages of a function, class, or symbol | `/ast-find` |
| Restoring remembered project context at session start | `/mem-recall` |
| Saving a decision or convention for later sessions | `/mem-save` |
| Clearing stored project memory | `/mem-clear` |
| Verifying an OpenSpec change after implementation | `spec-review` skill |

---

## ast-find — Structural Symbol Search

Powered by ast-grep. Use `/ast-find` when you want code-aware results for a symbol instead of plain-text matches from comments or strings.

| Command | Arguments | Description |
|---|---|---|
| `/ast-find` | `<symbol>` | Find definitions, imports, and call sites for a symbol |

### Examples

```text
/ast-find useQuery
/ast-find createServer
```

### Best for

- Tracing where a symbol is declared and used
- Mapping imports before a refactor
- Understanding how a function or class is wired through the codebase

---

## c7-* — Documentation (Context7)

Commands that query official library documentation via Context7. These are the retained docs lookup commands.

| Command | Arguments | Description |
|---|---|---|
| `/c7-docs` | `<library>` | High-level documentation overview for a library |
| `/c7-how` | `<question>` | Ask how to do something with a library |
| `/c7-api` | `<function> <library>` | Look up API signatures, parameters, and return values |

### Examples

```text
/c7-docs drizzle-orm
/c7-how handle file uploads in express
/c7-api useQuery react-query
```

### Best for

- Getting the official overview before implementation
- Checking recommended usage patterns from library docs
- Confirming exact API details without leaving the CLI

---

## mem-* — Project Memory

Commands that read and write the persistent memory knowledge graph. They run in the main context so recalled information is immediately available.

| Command | Arguments | Description |
|---|---|---|
| `/mem-save` | `<fact or decision>` | Store a project fact, convention, or decision |
| `/mem-recall` | — | Retrieve remembered project context |
| `/mem-clear` | — | Remove stored memory for this project |

### Examples

```text
/mem-save we use Zod for all input validation
/mem-save API handlers live in src/routes/
/mem-recall
```

### Best for

- Preserving decisions across sessions
- Restoring context before starting new work
- Clearing outdated memory when a project changes direction

---

## spec-review — OpenSpec Verification Skill

`spec-review` is a global skill, not a slash command. Load it when you want an implementation checked against an OpenSpec change.

The repo-local copy is retained in this cleanup and updated later to stay aligned with the reduced command and tool surface.

**Source:** [`skills/spec-review/SKILL.md`](../skills/spec-review/SKILL.md) in this repo.

**Install:** Symlink the skill directory into your global skills folder:

```bash
mkdir -p ~/.config/opencode/skills
ln -sfn "$(pwd)/skills/spec-review" ~/.config/opencode/skills/spec-review
```

To trigger it, ask the agent:

```text
Load the spec-review skill and review the add-dark-mode change
```

Or for the most recent non-archived change:

```text
Load the spec-review skill and review the current change
```

### What it checks

**Pass 1 - Spec compliance:**
Reads `openspec/changes/<name>/tasks.md` and uses ast-grep to verify each task is implemented. Reports done, incomplete, or missing work.

**Pass 2 - Review checks:**
- Reviews unfinished code indicators and other follow-up items flagged by the skill
- Runs structural review checks defined by the skill for the current change
