# OpenCode Workflow Examples

Concrete end-to-end workflows showing how to combine commands for common development scenarios.

For the full command references, see [opencode-commands.md](./opencode-commands.md) for shared `/repo-*`, `/ast-*`, `/gh-*`, `/c7-*`, and `/mem-*` commands, plus [openspec-commands.md](./openspec-commands.md) for OpenSpec commands.

---

## 1. Onboarding to an Unfamiliar Codebase

You've just cloned a repo you've never seen before.

```
/repo-overview
```
Get a high-level map: purpose, directory layout, technologies, entry points.

> Shared `/repo-*`, `/ast-*`, `/gh-*`, `/c7-*`, and `/mem-*` commands are documented in [opencode-commands.md](./opencode-commands.md).

```
/repo-stats
```
Understand scale — how many files, token count, largest files.

```
/repo-export
```
Optional when you want a reusable XML snapshot on disk for a future session, a handoff, or another tool. Use `/repo-export-slim` instead if you want a smaller structural snapshot, or pass a custom path such as `/repo-export artifacts/backend.xml`.

```
/repo-auth
/repo-routes
/repo-models
```
Deep-dive the three pillars: who can do what, what endpoints exist, what data looks like.

```
/mem-save this project uses X for auth, Y ORM, REST API in src/routes/
```
Save key findings so you don't have to re-derive them next session.

---

## 2. Starting a New Feature — Fast Path

Clear requirements, straightforward feature. Stay on the shortest OpenSpec path from context to archive.

```
/repo-overview
/repo-pack-slim
```
Understand the codebase before writing a proposal. Pack-slim gives you a compressed structural view.

```
/opsx:propose add user notification preferences
```
Create the full OpenSpec artifact set in one pass: `proposal.md`, `specs/`, `design.md`, and `tasks.md`. Review and tighten anything that looks off before implementation.

```
/c7-how store user preferences in postgres with drizzle-orm
/gh-docs drizzle-orm insert and update
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
/gh-docs drizzle-orm insert and update
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

```
/repo-overview
```
Understand the structure, stack, and entry points before touching anything.

```
/repo-auth
/repo-routes
/repo-models
```
Map the three pillars: existing auth patterns, live endpoints, and the data model.

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
/gh-docs pdfkit text and tables
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
/repo-errors
```
Check for related error handling issues nearby — bugs often have swallowed siblings.

```
/c7-fix jsonwebtoken verify options ignoreExpiration
/gh-fix jwt token expiry validation node.js
```
Look up known solutions if the bug is library-related.

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

```
/repo-overview
```
Re-orient quickly if needed.

Proceed with implementation. At the end of the session:

```
/mem-save we decided to use optimistic updates in the notifications UI
```
Capture new decisions for next time.

---

## 8. Debugging a Production Error

You have an error message or stack trace.

```
/c7-fix TypeError: Cannot read properties of undefined (reading 'map')
```
Check the official docs for what could cause this in the relevant library.

```
/gh-fix TypeError: Cannot read properties of undefined reading map react
```
Find how others solved the same error in real codebases.

```
/ast-find processNotifications
```
Find all call sites and definitions of the suspected function in your codebase.

```
/repo-errors
```
Audit the broader error handling and look for related swallowed failures.

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
/gh-examples zod schema validation with express middleware
```
See how production codebases integrate it, not just toy examples.

```
/gh-docs zod transform and refine
```
Combine official docs with real-world usage for the APIs you'll actually use.

---

## 10. Refactoring Existing Code

You want to rename a function or change a pattern across the codebase.

```
/ast-find fetchUserData
```
Find every definition, call site, and import of the target symbol.

```
/ast-refactor fetchUserData(userId)
```
Find all sites that match the current call shape so you know exactly what needs to change.

```
/ast useCallback($$$ARGS)
```
Run a broader structural search to verify you did not miss a related pattern.

```
/repo-review
```
After the refactor, catch TODOs, debug code, or type annotation gaps introduced along the way.

---

## 11. Upgrading a Dependency

Moving from one major version to another.

```
/c7-migrate next.js 14 to 15
```
Read the official migration guide: breaking changes, new APIs, and deprecations.

```
/gh-pattern next.js 15 app router
```
See how the community has already updated real projects.

```
/ast-find useRouter
```
Find all usages of the old API in your codebase to understand migration scope.

```
/repo-errors
```
After upgrading, check whether new error patterns were introduced.

---

## 12. Pre-commit Code Audit

Before opening a PR, make sure the code is clean.

```
/repo-review
```
Run a full audit for tech debt markers, debug leftovers, potential secrets, empty catches, and missing return types. Fix everything flagged as Critical or High.

If you worked through OpenSpec artifacts, also run:

- _"Load the spec-review skill and review the current change"_

---

## 13. Investigating an Unfamiliar Pattern

You see a pattern in the codebase you don't recognize.

```
/ast-find createServerAction
```
Find all usages in the current codebase to understand how it is being used locally.

```
/gh-examples createServerAction next.js
```
See real-world examples from other projects for broader context.

```
/c7-docs next.js server actions
```
Get the official explanation.

---

## 14. Saving Institutional Knowledge

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
