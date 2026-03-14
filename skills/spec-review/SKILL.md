---
name: spec-review
description: Review an OpenSpec change for spec compliance and code quality issues. Use after /opsx:apply when the user says "review the change", "verify implementation", "check before archiving", or mentions reviewing an OpenSpec change. Always use this before /opsx:archive.
compatibility: opencode
---

**Tools used:** ast-grep MCP, built-in file reading, built-in terminal (for text search)

## What I do

**Language scope:** The code quality checks below target JavaScript and TypeScript projects. For other languages, adapt the debug-leftover patterns (e.g., `print()` / `breakpoint()` for Python, `fmt.Println` for Go) and skip TypeScript-specific checks.

After `/opsx:apply`, perform a two-pass review before archiving:

**Pass 1 — Spec compliance**

1. Identify the change to review:
   - If a change name was provided, read `openspec/changes/<name>/tasks.md`
   - Otherwise, find the most recently modified non-archived change folder under `openspec/changes/`
2. For each task in `tasks.md`, use the ast-grep MCP tool to verify it is implemented in the codebase
3. Report each task as: done / incomplete / missing

**Pass 2 — Code quality**

1. Use the built-in terminal tool to run `grep -rn` across changed files for:
   - `TODO|FIXME|HACK` — unfinished work
   - `console.log|debugger` — debug leftovers
   - `password|secret|api_key|apikey|private_key` — potential hardcoded secrets
   Report file paths and line numbers for each match.
2. Use the `ast-grep` MCP tool to find:
   - Empty catch blocks: `try { $$$ } catch($E) {}`
   - Missing return type annotations on exported functions in typed languages (TypeScript, TypeScriptReact)

## Output format

Produce a single prioritized report:

1. Spec gaps (tasks incomplete or missing) — blocks archiving
2. Critical code issues (secrets, empty catches)
3. High (debug code, missing types)
4. Medium (TODO/FIXME markers)

Include file and line references for every finding.

## When to use me

Load this skill after `/opsx:apply` and before `/opsx:archive`. Run it on any OpenSpec change to catch spec drift and code quality issues before the change is archived.

You can also invoke it directly:

```
Load the spec-review skill and review the add-dark-mode change
```

Or for the most recent change:

```
Load the spec-review skill and review the current change
```
