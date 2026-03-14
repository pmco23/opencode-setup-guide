# Global OpenCode Rules

Personal defaults that apply to every project and session.

---

## Session start

At the start of a new session on an existing project, run `/mem-recall` before anything else.
This restores saved decisions, conventions, and gotchas from previous sessions without re-reading the codebase.

---

## Researching libraries

- For official API references and how-to questions, use `context7` MCP tools (`resolve-library-id` + `query-docs`) via `/c7-docs`, `/c7-how`, `/c7-api`

---

## Exploring the codebase

- Use `ast-grep` via `/ast-find` for precise structural queries: function definitions, call sites, imports, anti-patterns
- For unfamiliar codebases, explore key files directly before writing any code or proposals

---

## OpenSpec workflow

Before running `/opsx:propose` on an existing codebase:
1. Explore the codebase directly — read config files, browse key directories
2. Use `/ast-find` to discover auth, routing, and data model patterns
3. Save key findings with `/mem-save`
4. Seed `openspec/config.yaml` with architecture conventions before generating artifacts

For bug fixes, skip the full spec pipeline: use `/opsx:explore` to diagnose first,
then `/opsx:propose fix-<name>` with a minimal tasks.md (1-3 tasks).

---

## After implementing

After `/opsx:apply`, load the `spec-review` skill and run it before archiving.
Ask: "Load the spec-review skill and review the `<change-name>` change."

---

## Memory

Save to memory (`/mem-save`) whenever you discover:
- Architecture decisions or conventions
- Known bugs or gotchas in a dependency
- Project-specific patterns that would take time to re-derive