# OpenCode Workflow Examples

Concrete end-to-end workflows showing how to combine commands for common development scenarios.

For the full OpenSpec command reference, see [openspec-commands.md](./openspec-commands.md).

---

## 1. Onboarding to an Unfamiliar Codebase

You've just cloned a repo you've never seen before.

```
/repo-overview
```
Get a high-level map: purpose, directory layout, technologies, entry points.

```
/repo-stats
```
Understand scale — how many files, token count, largest files.

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

## 2. Starting a New Feature — Fast Path (OpenSpec)

Clear requirements, straightforward feature. Use `/opsx:propose` to go from idea to ready-to-implement in one step.

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
/spec-review add-user-notification-preferences
```
Optional second pass: catches debug leftovers, hardcoded secrets, empty catches.

> `spec-review` is a skill, not a slash command. Ask the agent: _"Load the spec-review skill and review the add-user-notification-preferences change"_

```
/opsx:archive
```
Syncs delta specs and moves the change to `openspec/changes/archive/`.

---

## 3. Starting a New Feature — Careful Path (OpenSpec)

Requirements are fuzzy or the feature is complex. Use explore → step-by-step artifact creation.

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

---

## 4. Closing a Parallel Sprint (OpenSpec)

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

---

## 5. Adding a Feature to an Existing Codebase (Brownfield)

You need to add something new to a codebase you didn't write. Map what exists before writing any proposal.

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
/spec-review add-invoice-export-to-pdf
```
Catch debug leftovers, hardcoded secrets, empty catches.

> `spec-review` is a skill. Ask the agent: _"Load the spec-review skill and review the add-invoice-export-to-pdf change"_

```
/opsx:archive
```

---

## 6. Bug Fix with OpenSpec

You have a bug report or failing test. Skip the full spec pipeline — use a lightweight propose with minimal tasks.

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
Write a minimal proposal: what's wrong, what fixes it, and a short `tasks.md` (often just 1–3 tasks). No need for full `specs/` or `design.md`.

```
/opsx:apply
```
Fix in a clean context.

```
/opsx:archive
```
Skip `/opsx:verify` for small single-file fixes. Run it if the bug touched shared infrastructure or has side effects across modules.

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

```
spec-review skill
```
If working with OpenSpec: verify all tasks are implemented before archiving. Ask the agent: _"Load the spec-review skill and review the current change"_

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
