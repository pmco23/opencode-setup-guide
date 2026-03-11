# Spec-Kit Installation Guide

Setting up Spec-Kit for use with OpenCode.

---

## Requirements

- Node.js (required for the shared OpenCode config: `repomix`, `memory`, `sequential-thinking`)
- Python 3.9 or higher
- [uv](https://github.com/astral-sh/uv) package manager
- An existing project directory
- Optional: Docker if you want the shared `ast-*` commands

Node.js and Docker are OpenCode shared-config prerequisites. Spec-Kit itself still uses Python + `uv`.

---

## 1. Install uv (if not already installed)

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Or via pip:

```bash
pip install uv
```

---

## 2. Install Spec-Kit

```bash
uv tool install specify-cli
```

Verify:

```bash
specify --version
```

---

## 3. Initialize in Your Project

Navigate to your project root and run:

```bash
cd your-project
specify init . --ai opencode
```

This creates the `.specify/` directory structure:

```
.specify/
├── memory/
│   └── constitution.md     # project architecture rules (fill this in)
├── scripts/
├── specs/                  # one folder per feature spec
│   └── NNN-feature-name/
│       ├── spec.md
│       ├── plan.md
│       ├── tasks.md
│       ├── data-model.md
│       ├── research.md
│       └── contracts/
└── templates/              # custom artifact templates
```

It also injects `/speckit.*` slash commands into OpenCode via framework-managed files in `.claude/skills/`. These are distinct from the manual OpenCode review skills installed under `~/.config/opencode/skills/`. Restart OpenCode after init.

---

## 4. Verify Commands Are Available in OpenCode

After `specify init`, type `/speckit.` in OpenCode — you should see the full command list:

```
/speckit.constitution
/speckit.clarify
/speckit.specify
/speckit.plan
/speckit.analyze
/speckit.tasks
/speckit.implement
/speckit.checklist
```

---

## 5. Write Your Constitution

Before running any `/speckit.*` command on a real project, fill in `.specify/memory/constitution.md`. This is the architectural rulebook Spec-Kit reads before generating every artifact.

A constitution for a typical TypeScript + PostgreSQL project:

```md
# Project Constitution

## Stack
TypeScript with strict mode.
Node.js API server using Express.
PostgreSQL database with Prisma ORM.
React frontend with Tailwind CSS and Zustand state management.

## Conventions
Input validation uses Zod — never trust raw req.body directly.
API handlers follow the repository pattern — no direct DB access in routes.
All database access goes through src/db/repositories/.
Authentication is JWT-based via src/middleware/auth.ts.

## Testing
Tests use Vitest. Every new module needs a corresponding test file.
Integration tests live in tests/integration/.

## Other
All new API endpoints must be documented in docs/api/.
```

The agent reads this before generating any artifact, so specs and tasks will reflect your actual architecture rather than generic boilerplate.

### Brownfield projects: seed constitution.md first

On an existing codebase, don't start with `/speckit.specify` — the spec will be generic. Run these first in OpenCode to discover what actually exists:

```
/repo-overview
/repo-auth
/repo-routes
/repo-models
/mem-save <key findings about the architecture>
```

Then use those findings to populate `.specify/memory/constitution.md`, and only then run `/speckit.specify`.

---

## 6. First Run

### New projects

Start with the constitution:

```
/speckit.constitution
```

Then write your first spec:

```
/speckit.specify your feature description here
```

Follow the full pipeline: `/speckit.plan` → `/speckit.analyze` → `/speckit.tasks` → `/speckit.implement`.

### Existing (brownfield) projects

Don't start with `/speckit.specify` on an unfamiliar codebase — the spec will be generic. Run these first in OpenCode:

```
/repo-overview
/repo-auth
/repo-routes
/repo-models
/mem-save <key findings about the architecture>
```

Then fill in `.specify/memory/constitution.md` with what you learned (see step 5), and only then run `/speckit.specify`.

### Bug fixes

Skip the full pipeline. Diagnose in plain conversation first:

```
[describe the bug and symptoms in a plain message]
```

Once you understand the cause, write a narrow spec:

```
/speckit.specify fix <description of root cause>
```

Keep `spec.md` short and scope it to just the fix. Skip the plan/data-model for a 1-3 task fix. Go straight to `/speckit.tasks` → `/speckit.implement`.

---

## Keeping Spec-Kit Updated

```bash
uv tool upgrade specify-cli
```

After upgrading, re-initialize to regenerate the OpenCode skills:

```bash
cd your-project
specify init . --ai opencode
```

Restart OpenCode.

---

## Environment Variables

| Variable | Purpose |
|---|---|
| `SPECIFY_FEATURE` | Override feature detection (useful in non-Git repos or CI) |

Example:
```bash
SPECIFY_FEATURE=add-dark-mode specify init . --ai opencode
```

---

## Useful CLI Commands

```bash
specify init . --ai opencode    # Initialize in a project
specify --version               # Check installed version
```

---

## Reference

- [Spec-Kit command reference](./speckit-commands.md) — full `/speckit.*` command reference
- [Workflow examples](./opencode-workflows.md) — end-to-end workflows combining OpenCode + Spec-Kit
- [Spec-Kit GitHub](https://github.com/github/spec-kit)
