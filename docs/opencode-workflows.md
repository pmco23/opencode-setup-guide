# OpenCode Workflow Examples

Concrete end-to-end workflows showing how to combine commands for common development scenarios.

For the full command references, see [opencode-commands.md](./opencode-commands.md) for shared `/repo-*`, `/ast-*`, `/gh-*`, `/c7-*`, and `/mem-*` commands, plus [openspec-commands.md](./openspec-commands.md) or [speckit-commands.md](./speckit-commands.md) for tool-specific commands.

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

Clear requirements, straightforward feature. Run through the spec pipeline in sequence.

### OpenSpec

```
/repo-overview
/repo-pack-slim
```
Understand the codebase before writing a proposal. Pack-slim for a compressed structural view.

```
/opsx:propose add user notification preferences
```
Creates `proposal.md`, `specs/`, `design.md`, and `tasks.md` in one pass. Review and edit artifacts if needed.

```
/c7-how store user preferences in postgres with drizzle-orm
/gh-docs drizzle-orm insert and update
```
Research the implementation approach. These run as subtasks — no context pollution.

```
/opsx:apply
```
Implement in a clean context window. Works through `tasks.md` task by task, checking off as it goes.

```
/opsx:verify
```
Validate completeness, correctness, and coherence against the artifacts before archiving.

```
spec-review skill
```
Optional second pass: catches debug leftovers, hardcoded secrets, empty catches.

> `spec-review` is a skill, not a slash command. Ask the agent: _"Load the spec-review skill and review the add-user-notification-preferences change"_

```
/opsx:archive
```
Syncs delta specs and moves the change to `openspec/changes/archive/`.

### Spec-Kit

```
/repo-overview
/repo-pack-slim
```
Understand the codebase before writing a spec. Pack-slim for a compressed structural view.

> If this is the first time using Spec-Kit in this project, run `/speckit.constitution` first, or rerun it whenever the architecture rules need a refresh.

```
/speckit.specify add user notification preferences
```
Define requirements → creates `.specify/specs/NNN-add-user-notification-preferences/spec.md`.

```
/speckit.plan
```
Technical plan → creates `plan.md`, `data-model.md`, and `contracts/` in the spec folder. Review and edit.

```
/speckit.tasks
```
Generates `tasks.md` from the plan. Review before implementing.

```
/c7-how store user preferences in postgres with drizzle-orm
/gh-docs drizzle-orm insert and update
```
Research the implementation approach. These run as subtasks — no context pollution.

```
/speckit.implement
```
Implement in a clean context window. Works through `tasks.md` task by task, checking off as it goes.

```
spec-review-sk skill
```
Verify implementation against `tasks.md` + code quality audit.

> `spec-review-sk` is a skill, not a slash command. Ask the agent: _"Load the spec-review-sk skill and review the NNN-add-user-notification-preferences spec"_

---

## 3. Starting a New Feature — Careful Path

Requirements are fuzzy or the feature is complex. Use exploration before committing to a plan.

### OpenSpec

```
/opsx:explore how should we structure the notification preferences?
```
Investigate the codebase, compare approaches, clarify requirements. No artifacts created yet.

Once direction is clear:

```
/opsx:new add-notification-preferences
```
Creates the change folder and metadata only.

```
/opsx:continue
```
Creates the first ready artifact (proposal). Review and edit it.

```
/opsx:continue
```
Creates the next artifact in dependency order (specs). Review.

```
/opsx:continue
/opsx:continue
```
Repeat until all planning artifacts are done (design, tasks).

```
/opsx:apply
```
Implement in a clean context.

```
/opsx:verify
/opsx:archive
```
Verify and archive.

### Spec-Kit

```
/speckit.clarify add notification preferences
```
Structured clarification pass — surfaces ambiguities before anything is written. No artifacts yet.

Once questions are answered:

```
/speckit.specify add notification preferences
```
Define requirements based on the clarified scope.

```
/speckit.plan
```
Technical plan. Review and edit `plan.md` and `data-model.md` carefully before proceeding.

```
/speckit.tasks
```
Generate `tasks.md` from the approved plan.

```
/speckit.analyze
```
Cross-artifact consistency check. Catches gaps between spec, plan, and tasks before you implement.

```
/speckit.implement
```
Implement in a clean context.

```
spec-review-sk skill
```
Post-implement verification.

---

## 4. Closing a Parallel Sprint / Checking Artifacts

### OpenSpec — Closing a Parallel Sprint

You've been running multiple changes in parallel and want to close them all out.

```
/opsx:verify fix-login-bug
/opsx:verify add-dark-mode
/opsx:verify update-footer
```
Verify each change independently first to surface any gaps.

```
/opsx:bulk-archive
```
Lists all completed changes, detects spec conflicts between them, resolves conflicts by inspecting what's actually in the codebase, and archives in chronological order.

### Spec-Kit — Checking Artifacts Before Implementing

You've generated spec artifacts and want to verify consistency before writing code.

```
/speckit.analyze
```
Cross-artifact check: verifies that `spec.md`, `plan.md`, `data-model.md`, and `contracts/` are mutually consistent. Surfaces contradictions and gaps.

```
/speckit.checklist
```
Quality checklist pass on the artifacts. Confirm everything is ready to implement.

Fix any flagged issues in the relevant artifact files, then run:

```
/speckit.implement
```

---

## 5. Adding a Feature to an Existing Codebase (Brownfield)

You need to add something new to a codebase you didn't write. Map what exists before writing any spec.

```
/repo-overview
```
Understand the structure, stack, and entry points before touching anything.

```
/repo-auth
/repo-routes
/repo-models
```
Map the three pillars: existing auth patterns, live endpoints (avoid collisions), and data model (understand before changing it).

```
/mem-save this project uses JWT in src/middleware/auth.ts, Prisma ORM, REST API under src/routes/
```
Save conventions and gotchas so you don't re-derive them next session.

### OpenSpec

Seed `openspec/config.yaml` with what you found — see [openspec-install.md § Brownfield projects](./openspec-install.md#brownfield-projects-seed-configyaml-first) for the format. This acts as a constitution: every OpenSpec proposal generated will respect the constraints you encode there.

```
/opsx:propose add invoice export to PDF
```
Write the spec informed by what actually exists. OpenSpec sees your `config.yaml` context.

```
/c7-how generate PDFs in node.js with pdfkit
/gh-docs pdfkit text and tables
```
Research the implementation approach as subtasks — no context pollution.

```
/opsx:apply
```
Implement in a clean context window. Works through `tasks.md` task by task.

```
/opsx:verify
```
Validate completeness against the spec before archiving.

```
spec-review skill
```
Catch debug leftovers, hardcoded secrets, empty catches.

> Ask the agent: _"Load the spec-review skill and review the add-invoice-export-to-pdf change"_

```
/opsx:archive
```

### Spec-Kit

Seed `.specify/memory/constitution.md` with what you found — see [speckit-install.md § Brownfield projects](./speckit-install.md#brownfield-projects-seed-constitutionmd-first) for the format. Every Spec-Kit artifact generated will respect the constraints you encode there.

```
/speckit.specify add invoice export to PDF
```
Write the spec informed by what actually exists. Spec-Kit sees your `constitution.md`.

```
/speckit.plan
```
Technical plan. Review `plan.md` and `data-model.md`.

```
/c7-how generate PDFs in node.js with pdfkit
/gh-docs pdfkit text and tables
```
Research the implementation approach as subtasks — no context pollution.

```
/speckit.tasks
```
Generate tasks.

```
/speckit.implement
```
Implement in a clean context window. Works through `tasks.md` task by task.

```
spec-review-sk skill
```
Catch debug leftovers, hardcoded secrets, empty catches.

> Ask the agent: _"Load the spec-review-sk skill and review the NNN-add-invoice-export-to-pdf spec"_

---

## 6. Bug Fix

You have a bug report or failing test. Skip the full spec pipeline.

### OpenSpec

```
/opsx:explore user sessions are expiring too early even with valid tokens
```
Investigate the codebase, trace the root cause. No artifacts created yet — pure diagnosis.

```
/ast-find verifyToken
```
Find all call sites and definitions of the suspected function to understand scope.

```
/repo-errors
```
Check for related error handling issues nearby — often bugs have swallowed siblings.

```
/c7-fix jsonwebtoken verify options ignoreExpiration
/gh-fix jwt token expiry validation node.js
```
Look up known solutions if the bug is library-related.

Once root cause is confirmed:

```
/opsx:propose fix-session-expiry
```
Write a minimal proposal: what's wrong, what fixes it, and a short `tasks.md` (often just 1-3 tasks). No need for full `specs/` or `design.md`.

```
/opsx:apply
```
Fix in a clean context.

```
/opsx:archive
```
Skip `/opsx:verify` for small single-file fixes. Run it if the bug touched shared infrastructure or has side effects across modules.

### Spec-Kit

Plain conversation:
```
I'm seeing user sessions expire too early even with valid tokens. Let's investigate.
```
Investigate the codebase, trace the root cause. No artifacts created yet — pure diagnosis.

```
/ast-find verifyToken
```
Find all call sites and definitions of the suspected function to understand scope.

```
/repo-errors
```
Check for related error handling issues nearby — often bugs have swallowed siblings.

```
/c7-fix jsonwebtoken verify options ignoreExpiration
/gh-fix jwt token expiry validation node.js
```
Look up known solutions if the bug is library-related.

Once root cause is confirmed, run a narrow spec:

```
/speckit.specify fix session expiry — tokens validated with wrong clock tolerance
```
Narrow scope: just the fix. Keep `spec.md` short. Skip plan/data-model for a 1-3 task fix.

```
/speckit.tasks
```
Minimal `tasks.md` — often just 1-3 tasks for a bug fix.

```
/speckit.implement
```
Fix in a clean context.

Skip `spec-review-sk` for small single-file fixes. Run it if the bug touched shared infrastructure or has side effects across modules.

---

## 7. Resuming Work on an Existing Project

Starting a new session on a project you've worked on before.

```
/mem-recall
```
Restore saved decisions, conventions, and gotchas from previous sessions — without re-reading the codebase.

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
Audit the broader error handling — find other swallowed errors that might be hiding related issues.

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
See how production codebases integrate it — not just toy examples.

```
/gh-docs zod transform and refine
```
Combined view: official docs + real-world usage for the specific APIs you'll use.

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
Find all sites that match the current call shape — exactly what needs to change.

```
/ast useCallback($$$ARGS)
```
Verify no related patterns were missed with a broader structural search.

```
/repo-review
```
After the refactor: catch any TODOs, debug code, or type annotation gaps introduced.

---

## 11. Upgrading a Dependency

Moving from one major version to another.

```
/c7-migrate next.js 14 to 15
```
Official migration guide: breaking changes, new APIs, deprecations.

```
/gh-pattern next.js 15 app router
```
See how the community has already updated their code to the new version.

```
/ast-find useRouter
```
Find all usages of the old API in your codebase to understand the scope of the change.

```
/repo-errors
```
After upgrading: check if any new error patterns were introduced.

---

## 12. Pre-commit Code Audit

Before opening a PR, make sure the code is clean.

```
/repo-review
```
Full audit: tech debt markers, debug leftovers, potential secrets, empty catches, missing return types. Fix everything flagged as Critical or High.

If working with a spec tool, verify all tasks are implemented before closing:

- **OpenSpec**: _"Load the spec-review skill and review the current change"_
- **Spec-Kit**: _"Load the spec-review-sk skill and review the current spec"_

---

## 13. Investigating an Unfamiliar Pattern

You see a pattern in the codebase you don't recognize.

```
/ast-find createServerAction
```
Find all usages in the current codebase to understand how it's being used locally.

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
