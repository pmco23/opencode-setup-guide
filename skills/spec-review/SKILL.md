---
name: spec-review
description: Two-pass post-implementation review: verify OpenSpec tasks.md compliance then audit code quality for debug leftovers, secrets, and empty catches
compatibility: opencode
---

## What I do

After `/opsx:apply`, perform a two-pass review before archiving:

**Pass 1 — Spec compliance**

1. Identify the change to review:
   - If a change name was provided, read `openspec/changes/<name>/tasks.md`
   - Otherwise, find the most recently modified non-archived change folder under `openspec/changes/`
2. For each task in `tasks.md`, use the ast-grep MCP tool to verify it is implemented in the codebase
3. Report each task as: done / incomplete / missing

**Pass 2 — Code quality**

1. Use the repomix MCP tool (`pack_codebase`) with `directory` set to the project root. After packing, copy the output file into the project directory (e.g. `cp <outputFilePath> ./repomix-output.md`) so it is accessible on the host filesystem.
2. Use `grep_repomix_output` (with the returned `outputId`) to scan for:
   - `TODO|FIXME|HACK` — unfinished work
   - `console.log|debugger` — debug leftovers
   - `password|secret|api_key|apikey|private_key` — potential hardcoded secrets
3. Use the ast-grep MCP tool to find:
   - Empty catch blocks: `try { $$$ } catch($E) {}`
   - Missing return type annotations on exported functions

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
