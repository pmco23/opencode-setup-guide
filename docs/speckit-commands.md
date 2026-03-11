# Spec-Kit Command Reference

Full reference for Spec-Kit's `/speckit.*` slash commands, used inside AI coding assistants (OpenCode, Claude Code, Cursor, Windsurf).

Source: [github/spec-kit](https://github.com/github/spec-kit)

For installation, see [speckit-install.md](./speckit-install.md).

---

## Quick Reference

| Command | Purpose |
|---|---|
| `/speckit.constitution` | Create or update `.specify/memory/constitution.md` — project architecture rules |
| `/speckit.clarify` | Structured clarification before committing to a spec |
| `/speckit.specify` | Define requirements → `spec.md` |
| `/speckit.plan` | Technical plan → `plan.md`, `data-model.md`, `contracts/` |
| `/speckit.analyze` | Cross-artifact consistency check |
| `/speckit.tasks` | Generate `tasks.md` from the plan |
| `/speckit.implement` | Execute tasks from `tasks.md` |
| `/speckit.checklist` | Quality checklist on artifacts or implementation |

---

## Commands

### `/speckit.constitution`

Create or update the project constitution at `.specify/memory/constitution.md`. This is the architectural rulebook that Spec-Kit reads before generating every artifact.

```
/speckit.constitution
```

**What it does:**
- Prompts you to describe (or review) your project's architecture, stack, conventions, and constraints
- Writes or updates `.specify/memory/constitution.md`
- All subsequent Spec-Kit commands respect this file

**When to use:**
- First time using Spec-Kit on a project
- After a major architectural change
- On brownfield projects, before your first `/speckit.specify` — run `/repo-overview`, `/repo-auth`, `/repo-routes`, `/repo-models` first to gather facts, then feed them into the constitution

**Tip:** Be specific. Vague constitutions produce generic specs. Include your ORM, auth pattern, test framework, validation library, and folder conventions.

---

### `/speckit.clarify`

Structured clarification pass before writing a spec. Surfaces ambiguities and design decisions before any artifacts are created.

```
/speckit.clarify [feature description]
```

**What it does:**
- Asks targeted questions about scope, edge cases, and design decisions
- No artifacts created yet — pure clarification
- Produces a shared understanding that informs the subsequent `/speckit.specify`

**When to use:** Requirements are unclear, the feature scope is fuzzy, or there are multiple valid approaches. Skip for simple, well-understood changes.

---

### `/speckit.specify`

Define requirements for a feature. The starting point of the Spec-Kit pipeline.

```
/speckit.specify [feature description]
```

**What it creates:**

```
.specify/specs/NNN-feature-name/
└── spec.md
```

**Example:**

```
/speckit.specify add user notification preferences
```

Creates `.specify/specs/001-add-user-notification-preferences/spec.md` with requirements, acceptance criteria, and scenarios.

**When to use:** Your default starting point for a new feature. Run after `/speckit.clarify` if requirements were unclear.

---

### `/speckit.plan`

Generate a technical plan from the spec. Produces architecture decisions, data model, and API contracts.

```
/speckit.plan
```

**What it creates:**

```
.specify/specs/NNN-feature-name/
├── plan.md          # architecture decisions and approach
├── data-model.md    # data structures and schemas
└── contracts/       # API contracts, interface definitions
```

**When to use:** After reviewing and approving `spec.md`. Review and edit the plan artifacts before generating tasks — the quality of your plan directly determines the quality of your tasks.

---

### `/speckit.analyze`

Cross-artifact consistency check. Verifies that spec, plan, data model, and contracts are mutually consistent.

```
/speckit.analyze
```

**What it does:**
- Reads all artifacts in the current spec folder
- Identifies contradictions between `spec.md`, `plan.md`, `data-model.md`, and `contracts/`
- Reports gaps where an artifact references something not defined elsewhere
- Surfaces risks and missing decisions

**When to use:** After `/speckit.plan` and before `/speckit.tasks` to catch issues early. Also useful after manually editing artifacts.

---

### `/speckit.tasks`

Generate an implementation checklist from the plan.

```
/speckit.tasks
```

**What it creates:**

```
.specify/specs/NNN-feature-name/
└── tasks.md
```

**When to use:** After approving the plan and running `/speckit.analyze`. Review `tasks.md` carefully — this is what `/speckit.implement` will follow.

---

### `/speckit.implement`

Execute the tasks in `tasks.md`. The implementation step.

```
/speckit.implement
```

**What it does:**
- Reads `tasks.md` and identifies incomplete tasks
- Works through tasks sequentially, writing code and creating files
- Marks tasks complete as it goes
- Can resume from where it left off if interrupted

**When to use:** After finalizing `tasks.md`. Always start in a **clean context window** — the planning steps (repomix, ast-grep searches) add significant tokens that can degrade implementation quality.

---

### `/speckit.checklist`

Quality checklist pass on the current spec artifacts or implementation.

```
/speckit.checklist
```

**What it does:**
- Reviews artifacts for completeness (are all sections filled? are scenarios covered?)
- After implementation: checks that code matches the spec and plan intent
- Surfaces items that are present but low-quality or incomplete

**When to use:** Before `/speckit.implement` to confirm artifacts are ready. Also useful as a lightweight post-implement check for small features.

---

## Artifact Structure

A fully planned Spec-Kit spec contains:

```
.specify/
├── memory/
│   └── constitution.md         # project architecture rules (shared across all specs)
├── specs/
│   └── NNN-feature-name/
│       ├── spec.md             # requirements and acceptance criteria
│       ├── plan.md             # technical approach and architecture decisions
│       ├── data-model.md       # data structures and schemas
│       ├── tasks.md            # implementation checklist
│       ├── research.md         # research notes (optional)
│       └── contracts/          # API contracts, interface definitions
└── templates/                  # custom artifact templates
```

Spec folders are numbered sequentially (`001-`, `002-`, etc.) and named after the feature.

---

## Decision Guide

| Situation | Command |
|---|---|
| First time in a project | `/speckit.constitution` |
| Starting a new feature | `/speckit.specify` |
| Requirements unclear | `/speckit.clarify` first |
| Want a technical plan | `/speckit.plan` |
| Verifying artifact consistency | `/speckit.analyze` |
| Ready to generate tasks | `/speckit.tasks` |
| Implementing | `/speckit.implement` |
| Final quality check | `/speckit.checklist` |
| Post-implement deep review | `spec-review-sk` skill |

### When NOT to run the full pipeline

The full pipeline (clarify → specify → plan → analyze → tasks → implement → checklist) is designed for features with meaningful design decisions. Skip or trim it for smaller work:

| Situation | Recommended approach |
|---|---|
| Bug fix (1-3 files) | Diagnose in plain conversation → narrow `/speckit.specify` → `/speckit.tasks` → `/speckit.implement` (skip plan/data-model) |
| Tiny fix (1 function, obvious) | Just implement it. No Spec-Kit needed. |
| Spike or experiment | Plain conversation only. Don't create a spec — you may throw it away. |
| Dependency bump with no API changes | Direct implementation. Spec-Kit adds no value here. |
| Feature touching shared infrastructure | Run the full pipeline — `/speckit.analyze` catches cross-module spec drift. |

**Rule of thumb:** If `tasks.md` would have only 1-2 tasks, the overhead of a full spec may not be worth it. Diagnose in conversation to confirm your approach, then implement directly. Reserve the full pipeline for changes where the design decisions are non-obvious or the implementation will take more than an hour.

---

## Troubleshooting

| Problem | Solution |
|---|---|
| Commands not recognized | Run `specify init . --ai opencode` and restart OpenCode |
| Spec artifacts incomplete or generic | Fill in `.specify/memory/constitution.md` with project architecture details |
| Wrong spec folder targeted | Spec-Kit uses the most recently modified folder; specify the number prefix explicitly if needed |
| `SPECIFY_FEATURE` not detecting feature | Set `SPECIFY_FEATURE=feature-name` env var to override feature detection (useful in non-Git environments) |
| Plan contradicts spec | Run `/speckit.analyze` to surface the contradiction, then manually edit the relevant artifact |
