---
name: code-review
description: Audit staged or recent code changes for quality issues before committing. Use whenever the user says "review code", "check before commit", "audit changes", "pre-commit check", or wants a quality scan on files that did not go through OpenSpec. For OpenSpec changes, use spec-review instead.
compatibility: opencode
---

**Tools used:** ast-grep MCP, built-in file reading, built-in terminal (for text search)

## What I do

Run a code quality audit on recently changed files. This skill is for changes that did not go through OpenSpec — for OpenSpec changes, use `spec-review` instead.

**Language scope:** The checks below show JavaScript/TypeScript patterns as defaults. For other languages, adapt the debug-leftover patterns (e.g., `print()` / `breakpoint()` for Python, `fmt.Println` for Go) and skip TypeScript-specific checks.

### Step 1 — Identify files to review

1. If the user provided a file list or directory, use that
2. Otherwise, run `git diff --name-only` to find uncommitted changes
3. If no uncommitted changes, run `git diff --name-only HEAD~1` to review the last commit
4. Detect the primary language from file extensions

### Step 2 — Text-based checks

Use the built-in terminal tool to run `grep -rn` across the identified files for:

- `TODO|FIXME|HACK` — unfinished work markers
- Language-appropriate debug leftovers:
  - JS/TS: `console.log|debugger`
  - Python: `print(|breakpoint(|import pdb`
  - Go: `fmt.Println` (in non-log files)
- `password|secret|api_key|apikey|private_key` — potential hardcoded secrets

Report file paths and line numbers for each match.

### Step 3 — Structural checks

Use the `ast-grep` MCP tool to find:

- Empty catch blocks: `try { $$$ } catch($E) {}`
- For TypeScript files: missing return type annotations on exported functions

### Output format

Produce a single prioritized report:

1. **Critical** — hardcoded secrets or credentials
2. **High** — empty error handlers, debug code left in
3. **Medium** — TODO/FIXME markers

Include file path and line number for every finding.

End with a one-line summary: `N issues found (X critical, Y high, Z medium)` or `No issues found.`

## When to use me

- Before committing changes that did not go through OpenSpec
- As a quick quality check on any set of files
- When asked to "review", "audit", or "check" code before a commit or PR

You can invoke it directly:

```
Load the code-review skill and check my staged changes
```

Or for a specific directory:

```
Load the code-review skill and audit src/api/
```
