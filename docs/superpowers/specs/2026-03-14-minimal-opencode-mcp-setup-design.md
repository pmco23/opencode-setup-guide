# Minimal OpenCode MCP Setup Design

## Goal

Reduce the personal OpenCode setup to a minimal, high-value stack for an OpenSpec-oriented workflow by keeping only the MCPs and commands that add clear capability beyond what OpenCode already provides out of the box.

## Decisions

- Optimize for personal day-to-day use, not generic repo defaults for other users.
- Prefer a minimal setup with fewer moving parts over a research-heavy setup.
- Keep `memory` because explicit cross-session project recall is still valuable.
- Keep `context7` because fast official docs lookup is a real recurring need.
- Keep `ast-grep` because structural search is the most useful repo-analysis capability for this workflow.
- Keep `spec-review` as an important OpenSpec workflow skill.
- Drop `gh_grep`, `repomix`, and `sequential-thinking` from the minimal default now.

## Recommended Approach

Use a minimal capability-first setup:

1. Keep only MCPs that add distinct value beyond native OpenCode behavior.
2. Remove MCPs whose main benefit is convenience, redundant orchestration, or occasional exploration.
3. Trim the custom command surface so the surviving commands map directly to retained MCPs and the OpenSpec review workflow.

This approach is preferred because the primary workflow is implementation in repos already understood reasonably well, not broad repository ingestion or example mining. The design keeps the tools that solve real recurring gaps while removing the ones that mostly add ceremony.

## Scope

### Keep

- `memory`
- `context7`
- `ast-grep`
- `spec-review`
- The smallest command surface that makes those capabilities easy to use

### Remove or Demote

- `sequential-thinking`
- `repomix`
- `gh_grep`
- Commands that exist mainly to orchestrate those removed MCPs

### Re-evaluate Separately

- `@tarquinen/opencode-dcp@latest`

The DCP plugin is not part of the MCP decision itself. It should be judged as a workflow-noise versus context-management tradeoff rather than a core capability question.

## Native OpenCode Coverage

### Thinking and Planning

OpenCode already provides substantial built-in planning behavior:

- `plan` mode for analysis without editing
- `build` mode for normal implementation
- built-in subagents such as planning, general-purpose, and exploration agents
- automatic context compaction and summarization for long sessions
- project and global instructions through `AGENTS.md` and config/rules

Because of that, an external forced step-by-step reasoning MCP is not required for the target workflow. `sequential-thinking` may still be useful for users who want stricter prompting discipline, but it is not necessary as a default in this personal minimal setup.

### Memory

OpenCode already retains session context, supports compaction, and can load project instructions. However, those features do not replace explicit persistent memory that can be deliberately saved, recalled, and cleared across sessions.

That distinction is the reason to keep `memory`. It adds a capability OpenCode does not natively provide in the same form.

## MCP Decision Matrix

### Keep

#### `memory`

- **Why keep it:** explicit cross-session project recall remains valuable.
- **What it adds beyond OpenCode:** deliberate save/recall/clear behavior for project facts and conventions.
- **Role in the workflow:** preserve architecture decisions, conventions, and known gotchas between sessions.

#### `context7`

- **Why keep it:** official docs lookup is a recurring need.
- **What it adds beyond OpenCode:** direct, structured library documentation lookup instead of relying on generic web fetches or model memory.
- **Role in the workflow:** quick API reference, migration guidance, and how-to lookups during implementation.

#### `ast-grep`

- **Why keep it:** structural search is the strongest real analysis need in this workflow.
- **What it adds beyond OpenCode:** syntax-aware code search for definitions, call sites, imports, and refactor targets.
- **Role in the workflow:** targeted investigation during implementation in existing repos.

### Remove

#### `sequential-thinking`

- **Why remove it:** it overlaps too much with OpenCode's built-in planning tools for this workflow.
- **What is lost:** a dedicated external tool that forces explicit step-by-step reasoning.
- **Why that loss is acceptable:** the workflow already relies on OpenCode planning behavior and direct prompting more than on formal reasoning traces.

#### `repomix`

- **Why remove it:** it is most valuable for broad repository ingestion and canned exploration flows, which are not the main personal use case.
- **What is lost:** repo-wide packed snapshots, export flows, and command wrappers built around them.
- **Why that loss is acceptable:** the target workflow is mostly implementation in repos already understood well enough not to require heavy ingestion tooling by default.

#### `gh_grep`

- **Why remove it:** GitHub example mining is only occasionally useful rather than core.
- **What is lost:** dedicated public-code search commands for pattern discovery and fix hunting.
- **Why that loss is acceptable:** occasional real-world examples do not justify carrying the tool as part of a minimal default stack.

## Command-Layer Design

### Keep

#### `mem-*`

- Keep `mem-save`, `mem-recall`, and `mem-clear`.
- `mem-save` and `mem-recall` are the primary value.
- `mem-clear` is administrative but still appropriate when memory is intentionally retained.

#### `c7-*`

- Keep `c7-docs`, `c7-how`, and `c7-api`.
- Drop `c7-fix` and `c7-migrate` from the minimal default.
- This keeps the official-docs capability while avoiding a larger command surface than the workflow requires.

#### Minimal `ast-*`

- Keep `ast-find` as the clearest expression of the structural-search need.
- Drop raw/power-user commands such as `ast`, `ast-lang`, and `ast-refactor` from the minimal default.
- This makes the minimal command surface explicit instead of leaving structural-search commands open-ended.

#### `spec-review`

- Keep it as an important OpenSpec workflow skill.
- It is not MCP bloat; it is a workflow quality gate that still matters in the intended setup.

### Remove

#### `repo-*`

- Remove the whole family in a minimal setup because it depends heavily on `repomix` and supports a broader exploration workflow than the target use case needs.

#### `gh-*`

- Remove the whole family if `gh_grep` is removed.

#### Power-user `ast-*` commands

- Remove commands such as raw pattern search and refactor-target wrappers from the minimal default.
- The minimal default favors `ast-find` over a larger structural-search command catalog.

## Risks and Tradeoffs

### Accepted Tradeoffs

- Less push-button support for unfamiliar codebase exploration
- Less direct access to public GitHub usage examples
- Fewer canned repo-analysis workflows

These tradeoffs are acceptable because they optimize for the actual day-to-day workflow rather than theoretical maximum capability.

### Failure Conditions

The minimal setup should be reconsidered if any of the following become common:

- repeated need for repo-wide ingestion or snapshot exports
- frequent need for real-world public code examples during implementation
- repeated planning failures that show OpenCode's built-in planning is not disciplined enough without a dedicated reasoning MCP

If those patterns become normal rather than occasional, the removed MCPs should be re-evaluated.

## Expected End State

The personal setup becomes a smaller, clearer OpenCode configuration with these defaults:

- Keep MCPs: `memory`, `context7`, `ast-grep`
- Drop MCPs: `repomix`, `gh_grep`, `sequential-thinking`
- Keep workflow skill: `spec-review`
- Keep commands: `mem-*`, `c7-docs`, `c7-how`, `c7-api`, `ast-find`, and `spec-review`
- Drop commands: `repo-*`, `gh-*`, `c7-fix`, `c7-migrate`, `ast`, `ast-lang`, `ast-refactor`, and other command wrappers that no longer map to a retained core capability

The result is a setup where OpenCode handles planning, editing, and normal repo work natively, while MCPs are reserved for the few capabilities that still add distinct value: persistent memory, official docs lookup, and structural search.
