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

## Spec-Kit workflow

Before running `/speckit.specify` on an existing codebase:
1. Run `/repo-overview`, `/repo-auth`, `/repo-routes`, `/repo-models` to map what exists
2. Save key findings with `/mem-save`
3. Seed `.specify/memory/constitution.md` with architecture conventions before generating artifacts

For bug fixes, skip the full pipeline: have a plain conversation to diagnose first,
then run a narrow `/speckit.specify` scoped only to the fix (1-3 tasks in `tasks.md`).

---

## After implementing

After `/speckit.implement`, load the `spec-review-sk` skill and run it before closing the feature.
Ask: "Load the spec-review-sk skill and review the `<NNN-feature-name>` spec."

---

## Memory

Save to memory (`/mem-save`) whenever you discover:
- Architecture decisions or conventions
- Known bugs or gotchas in a dependency
- Project-specific patterns that would take time to re-derive
