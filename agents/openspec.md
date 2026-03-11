# Global OpenCode Rules

Personal defaults that apply to every project and session.

---

## Session start

At the start of a new session on an existing project, run `/mem-recall` before anything else.
This restores saved decisions, conventions, and gotchas from previous sessions without re-reading the codebase.

---

## Multi-step tasks

When implementing a feature, refactoring, or debugging something with more than 2 steps,
use the `sequential-thinking` MCP tool to plan before acting.

---

## Researching libraries

- For official API references and how-to questions, use the `context7` MCP tools (`resolve-library-id` + `query-docs`)
- For real-world usage patterns and debugging errors, use the `gh_grep` MCP tool
- When both are relevant (validating docs against real usage), run both

---

## Exploring the codebase

- Use `repomix` (`pack_codebase`) for broad discovery: file structure, topic searches, config values, comments
- Use `ast-grep` for precise structural queries: function definitions, call sites, imports, anti-patterns
- For unfamiliar codebases, run `/repo-overview` before writing any code or proposals

---

## OpenSpec workflow

Before running `/opsx:propose` on an existing codebase:
1. Run `/repo-overview`, `/repo-auth`, `/repo-routes`, `/repo-models` to map what exists
2. Save key findings with `/mem-save`
3. Seed `openspec/config.yaml` with architecture conventions before generating artifacts

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
