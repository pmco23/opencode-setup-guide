# Remove Speckit References Design

## Goal

Reposition `opencode-setup-guide` as an OpenSpec-only setup guide by removing all Speckit-specific content, simplifying shared documentation, and ensuring the surviving docs read as a single-tool repository.

## Decisions

- Make the repo OpenSpec-only.
- Perform a strict purge of Speckit references rather than preserving historical mentions in active docs.
- Remove Speckit-specific files entirely instead of renaming or retaining compatibility stubs.
- Simplify shared documentation now that the dual-tool split is gone.

## Recommended Approach

Use a full OpenSpec rewrite for the repository surface:

1. Delete Speckit-only assets.
2. Rewrite shared docs so they describe one OpenSpec path instead of parallel OpenSpec and Speckit flows.
3. Run a repo-wide cleanup pass to remove any leftover Speckit names, commands, links, and review-skill references.

This approach is preferred because it matches the requested strict purge, avoids stale references hidden in shared docs, and leaves the repo easier to understand than a partial cleanup would.

## Scope

### Remove

- `agents/speckit.md`
- `docs/speckit-install.md`
- `docs/speckit-commands.md`
- `skills/spec-review-sk/`

### Rewrite

- `README.md`
- `docs/opencode-setup.md`
- `docs/opencode-workflows.md`
- `docs/opencode-commands.md`
- `agents/openspec.md` if it still uses generic multi-tool wording
- Any other tracked repository file surfaced by the cleanup search, except this design spec and any later planning artifacts created to execute it

### Keep

- `docs/openspec-install.md`
- `docs/openspec-commands.md`
- OpenSpec-specific guidance already present in shared docs
- Shared OpenCode configuration guidance that remains valid without Speckit

## Document-Level Design

### `README.md`

- Remove the two-tool comparison table.
- Remove the Speckit install path, Speckit AGENTS preset, and `spec-review-sk` references.
- Rewrite Quick Start as a single OpenSpec-only flow.
- Rewrite the index so it lists only surviving OpenSpec and shared docs.

### `docs/opencode-setup.md`

- Remove Speckit-specific prerequisites, comparisons, workflow branches, and examples.
- Keep shared MCP and OpenCode integration guidance that still applies.
- Reframe the doc around OpenSpec-specific workflow recommendations.

### `docs/opencode-workflows.md`

- Remove parallel Speckit branches from every workflow.
- Simplify each scenario so it presents one OpenSpec path.
- Ensure the resulting workflows still cover the current use cases without dual-tool language.

### `docs/opencode-commands.md`

- Remove the `spec-review-sk` section.
- Remove any scenario quick-reference entries mentioning `/speckit.*`.
- Keep shared OpenCode command documentation intact.

### Shared Language Cleanup

- Replace generic wording such as "spec tool" or "either tool" with explicit OpenSpec terminology where that improves clarity.
- Remove dead links and orphaned references introduced by deleting Speckit files.

## Cleanup Mechanics

### Data Flow

The cleanup is documentation-only and repo-local:

1. Delete Speckit-only files.
2. Rewrite shared documents so all surviving navigation paths point to OpenSpec-only content.
3. Perform a repo-wide search to catch leftover references.

### Error Handling

- Treat broken links and leftover Speckit mentions as cleanup failures.
- After deletions, search the tracked repository for `speckit`, `Spec-Kit`, `/speckit`, and `spec-review-sk`, excluding this design spec and any later planning artifacts created to execute it.
- For each remaining match, either rewrite the content to OpenSpec-only wording or remove the source entirely.

### Verification

Acceptance is based on documentation integrity rather than runtime behavior:

- Deleted files are no longer referenced anywhere in tracked repository content, excluding this design spec and any later planning artifacts created to execute it.
- Shared docs read cleanly as OpenSpec-only docs.
- Repo-wide search across tracked repository content shows zero remaining Speckit references outside this design spec and any later planning artifacts created to execute it.
- Navigation between surviving docs is verified by checking every edited markdown file for links to deleted Speckit paths and confirming the remaining internal links point only to files that still exist.

## Expected End State

The repository reads as an OpenSpec-only setup guide with simpler navigation, one consistent workflow story, and no remaining Speckit-specific content or naming.
