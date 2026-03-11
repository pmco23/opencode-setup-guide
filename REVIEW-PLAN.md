# Review Findings & Fix Plan

Audit of the repo performed on 2026-03-11. This file now records the agreed decisions from the review discussion and the cleanup applied in this pass.

---

## Resolved Before This Pass

### 1. Invalid JSON in `opencode.json`

**Files:** `opencode.json`
**Decision:** Remove the stale trailing-comma finding from the active plan. The file now parses as valid JSON.
**Status:** resolved

---

## Implemented in This Pass

### 2. `/repo-pack` and `/repo-pack-slim` now match actual MCP behavior

**Files:** `opencode.json`, `docs/opencode-commands.md`
**Decision:** Remove unsupported `outputPath` and fixed-file-path claims. Describe both commands as in-session repomix analysis commands rather than durable file exports.
**Status:** done

### 3. Shared prerequisites are called out earlier

**Files:** `README.md`, `docs/opencode-setup.md`, `docs/speckit-install.md`
**Decision:** Explain that Node.js is required for the shared OpenCode config, Docker is optional for `ast-*`, and Python + `uv` are only needed for Spec-Kit itself.
**Status:** done

### 4. Skill location differences are clarified

**Files:** `docs/opencode-setup.md`, `docs/openspec-install.md`, `docs/speckit-install.md`
**Decision:** Clarify the difference between framework-generated slash-command skills in `.claude/skills` and OpenCode skills in `~/.config/opencode/skills` and `.opencode/skills`.
**Status:** done

### 5. `agents/*` are treated as global OpenCode AGENTS presets

**Files:** `README.md`, `docs/opencode-setup.md`
**Decision:** Keep `agents/openspec.md` and `agents/speckit.md` as personal global defaults copied to `~/.config/opencode/AGENTS.md`, while documenting project `AGENTS.md` as a separate optional team-shared layer.
**Status:** done

### 6. Missing bootstrap directory creation was added

**Files:** `README.md`, `docs/opencode-commands.md`, `docs/opencode-setup.md`
**Decision:** Add `mkdir -p` steps before copying config or symlinking skills into `~/.config/opencode/...` so clean-machine setup works reliably.
**Status:** done

### 7. Workflow docs now point to shared command docs

**Files:** `docs/opencode-workflows.md`
**Decision:** Add a minimal navigation fix linking `docs/opencode-commands.md` from the workflow intro and one early reminder where shared `/repo-*` commands first appear.
**Status:** done

### 8. DCP stays in the shared config and is now documented

**Files:** `docs/opencode-setup.md`, `opencode.json`
**Decision:** Keep `@tarquinen/opencode-dcp@latest` as an opinionated default and document what it does, why this repo includes it, its tradeoffs, and how to tune or disable it.
**Status:** done

### 9. Context7 naming drift was corrected

**Files:** `opencode.json`, `agents/openspec.md`, `agents/speckit.md`
**Decision:** Replace outdated `get-library-docs` references with `query-docs` in shared command templates and agent presets.
**Status:** done

### 10. Spec-Kit fast-path workflow now states the constitution precondition

**Files:** `docs/opencode-workflows.md`
**Decision:** Add a minimal note that `/speckit.constitution` should be run once per project, or whenever architecture rules need refreshing, before the first `/speckit.specify`.
**Status:** done

### 11. README skill links now cover both review skills

**Files:** `README.md`
**Decision:** Replace the OpenSpec-only skills usage link with explicit links to both `spec-review` and `spec-review-sk` documentation sections.
**Status:** done

---

## Follow-up Enhancement

### A. Explicit export-to-disk repomix commands were added

**Files:** `opencode.json`, `docs/opencode-commands.md`
**Decision:** Add dedicated `/repo-export` and `/repo-export-slim` commands that use the Repomix CLI to write reusable XML files to disk with clear default output paths.
**Status:** done

---

## Priority Used for This Pass

1. Item 2
2. Item 6
3. Item 3
4. Item 5
5. Item 9
6. Item 4
7. Item 8
8. Item 10
9. Item 11
10. Item 7
