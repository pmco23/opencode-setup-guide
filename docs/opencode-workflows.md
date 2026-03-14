# OpenCode Workflow Examples

Concrete end-to-end workflows showing how to combine commands for common development scenarios.

For the full command references, see [opencode-commands.md](./opencode-commands.md) for `/c7-*` and `/mem-*` commands, plus [openspec-commands.md](./openspec-commands.md) for OpenSpec commands.

---

## 1. Onboarding to an Unfamiliar Codebase

You've just cloned a repo you've never seen before.

Start with direct investigation using the codebase itself:

```bash
# Read the README and key config files
ls -la
cat package.json  # or pyproject.toml, Cargo.toml, etc.
cat README.md
```

Understand the purpose, technologies, and entry points.

Use `/ast-find` to discover key patterns:

```
/ast-find main
/ast-find router
/ast-find handler
```

Find the core concepts and entry points.

```
/mem-save this project uses X for auth, Y ORM, REST API in src/routes/
```

Save key findings so you don't have to re-derive them next session.

---

## 2. Starting a New Feature — Fast Path

Clear requirements, straightforward feature. Stay on the shortest OpenSpec path from context to archive.

```
/ast-find handler
```

Find relevant handlers or entry points to understand where your feature fits.

```
/opsx:propose add user notification preferences
```

Create the full OpenSpec artifact set in one pass: `proposal.md`, `specs/`, `design.md`, and `tasks.md`. Review and tighten anything that looks off before implementation.

```
/c7-how store user preferences in postgres with drizzle-orm
```

Research the implementation approach without polluting the main context window.

```
/opsx:apply
```

Implement in a clean context window. Work through `tasks.md` task by task and check them off as you go.

```
/opsx:verify
```

Validate completeness, correctness, and coherence against the artifacts before you close the change.

```
spec-review skill
```

Optional second pass to catch debug leftovers, hardcoded secrets, and empty catches.

> `spec-review` is a skill, not a slash command. Ask the agent: _"Load the spec-review skill and review the add-user-notification-preferences change"_

```
/opsx:archive
```

Sync delta specs and move the change into `openspec/changes/archive/`.

---

## 3. Starting a New Feature — Careful Path

Requirements are fuzzy or the feature is complex. Explore first, then move through one OpenSpec path deliberately.

```
/opsx:explore how should we structure the notification preferences?
```

Investigate the codebase, compare approaches, and clarify requirements before generating artifacts.

Once direction is clear:

```
/opsx:new add-notification-preferences
```

Create the change folder and metadata only.

```
/opsx:continue
```

Generate the first ready artifact (`proposal.md`). Review and edit it.

```
/opsx:continue
```

Generate the next artifact in dependency order (`specs/`). Review again.

```
/opsx:continue
/opsx:continue
```

Keep going until all planning artifacts are ready (`design.md`, then `tasks.md`).

```
/c7-how store user preferences in postgres with drizzle-orm
```

Use shared research commands anywhere along the path when you need implementation detail before locking the plan.

```
/opsx:apply
```

Implement in a clean context once the artifacts are stable.

```
/opsx:verify
/opsx:archive
```

Verify the change, then archive it.

---

## 4. Closing a Parallel Sprint / Checking Artifacts

You've been running multiple OpenSpec changes in parallel and want to close them cleanly.

```
/opsx:verify fix-login-bug
/opsx:verify add-dark-mode
/opsx:verify update-footer
```

Verify each change independently first so gaps surface before archive time.

```
/opsx:bulk-archive
```

List completed changes, detect spec conflicts, reconcile them against the actual codebase, and archive in chronological order.

If verification finds gaps, fix the relevant artifact or implementation, then rerun `/opsx:verify` before archiving.

---

## 5. Adding a Feature to an Existing Codebase (Brownfield)

You need to add something new to a codebase you didn't write. Map what exists before you write the change.

Explore the codebase directly:

```bash
# Understand structure
find . -type f -name "*.ts" | head -20
cat package.json
```

Use `/ast-find` to locate key patterns:

```
/ast-find authenticate
/ast-find router
/ast-find schema
```

Find authentication, routing, and data model patterns.

```
/mem-save this project uses JWT in src/middleware/auth.ts, Prisma ORM, REST API under src/routes/
```

Save conventions and gotchas so you don't have to re-derive them next session.

Seed `openspec/config.yaml` with what you found — see [openspec-install.md § Brownfield projects](./openspec-install.md#brownfield-projects-seed-configyaml-first) for the format. This acts like a project constitution: generated artifacts should respect the constraints you encode there.

```
/opsx:propose add invoice export to PDF
```

Write the change against what actually exists. OpenSpec uses your `config.yaml` context.

```
/c7-how generate PDFs in node.js with pdfkit
```

Research the implementation approach as subtasks.

```
/opsx:apply
```

Implement in a clean context window. Work through `tasks.md` task by task.

```
/opsx:verify
```

Validate completeness against the spec before closing the change.

```
spec-review skill
```

Optional quality pass for debug leftovers, hardcoded secrets, and empty catches.

> Ask the agent: _"Load the spec-review skill and review the add-invoice-export-to-pdf change"_

```
/opsx:archive
```

---

## 6. Bug Fix

You have a bug report or failing test. Keep the OpenSpec path narrow and focused on the fix.

```
/opsx:explore user sessions are expiring too early even with valid tokens
```

Investigate the codebase and trace the root cause before creating artifacts.

```
/ast-find verifyToken
```

Find all call sites and definitions of the suspected function to understand scope.

```
/c7-docs jsonwebtoken verify options
```

Check official docs for the relevant library behavior.

Once the root cause is confirmed:

```
/opsx:propose fix-session-expiry
```

Write a minimal proposal describing the bug, the fix, and a short `tasks.md` list. For a small fix, you usually do not need a heavyweight design.

```
/opsx:apply
```

Fix it in a clean context.

```
/opsx:archive
```

Skip `/opsx:verify` for a tiny single-file fix. Run it if the bug touched shared infrastructure or has side effects across modules.

---

## 7. Resuming Work on an Existing Project

Starting a new session on a project you've worked on before.

```
/mem-recall
```

Restore saved decisions, conventions, and gotchas from previous sessions without re-reading the codebase.

Proceed with implementation. At the end of the session:

```
/mem-save we decided to use optimistic updates in the notifications UI
```

Capture new decisions for next time.

---

## 8. Debugging a Production Error

You have an error message or stack trace.

```
/c7-docs TypeError react map undefined
```

Look up what could cause this in the relevant framework or library docs.

```
/ast-find processNotifications
```

Find all call sites and definitions of the suspected function in your codebase.

```
/ast-find map
```

Broaden the search if the first target was too narrow.

---

## 9. Adopting a New Library

You want to add a library you haven't used before.

```
/c7-docs zod
```

Get the official overview: what it is, core concepts, basic usage.

```
/c7-how validate nested objects with zod
```

Targeted how-to for your specific use case.

```
/c7-api zod transform
```

Detailed API documentation for specific methods.

---

## 10. Refactoring Existing Code

You want to rename a function or change a pattern across the codebase.

```
/ast-find fetchUserData
```

Find every definition, call site, and import of the target symbol.

```
/ast-find fetchUserData(userId)
```

Find all sites with a specific call pattern to plan precise changes.

```
/mem-save refactoring fetchUserData -> getUserData in progress, src/api/ done, src/handlers/ pending
```

Track refactoring progress for complex multi-file changes.

---

## 11. Pre-commit Code Audit

Before opening a PR, make sure the code is clean.

**For non-OpenSpec changes**, use the `code-review` skill:

_"Load the code-review skill and check my staged changes"_

**For OpenSpec changes**, use the `spec-review` skill instead:

_"Load the spec-review skill and review the current change"_

Both catch debug leftovers, hardcoded credentials, empty catches, and missing return types. `spec-review` additionally verifies spec compliance against `tasks.md`.

---

## 12. Investigating an Unfamiliar Pattern

You see a pattern in the codebase you don't recognize.

```
/ast-find createServerAction
```

Find all usages in the current codebase to understand how it is being used locally.

```
/c7-docs next.js server actions
```

Get the official explanation.

---

## 13. Saving Institutional Knowledge

Capturing important decisions and conventions for the team (or future you).

```
/mem-save all database queries go through the repository layer in src/db/repositories/
/mem-save we use Zod for all input validation — never trust raw req.body directly
/mem-save the notification service has a known bug with timezone offsets, workaround: always store UTC
/mem-save library X v3.2 has a memory leak with long-running connections, pinned to v3.1
```

At the start of any new session:

```
/mem-recall
```

All of the above is immediately available without re-reading docs or code.