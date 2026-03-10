# OpenSpec Command Reference

Full reference for OpenSpec's `/opsx:*` slash commands, used inside AI coding assistants (OpenCode, Claude Code, Cursor, Windsurf).

Source: [Fission-AI/OpenSpec — docs/commands.md](https://github.com/Fission-AI/OpenSpec/blob/main/docs/commands.md)

For installation, see [openspec-install.md](./openspec-install.md).

---

## Quick Reference

### Core Profile (default)

| Command | Purpose |
|---|---|
| `/opsx:propose` | Create a change and generate all planning artifacts in one step |
| `/opsx:explore` | Think through ideas before committing to a change |
| `/opsx:apply` | Implement tasks from the change |
| `/opsx:archive` | Archive a completed change |

### Expanded Workflow Commands

These require enabling the `workflows` profile via `openspec config profile` + `openspec update`.

| Command | Purpose |
|---|---|
| `/opsx:new` | Start a new change scaffold (folder only) |
| `/opsx:continue` | Create the next artifact based on dependencies |
| `/opsx:ff` | Fast-forward: create all planning artifacts at once |
| `/opsx:verify` | Validate implementation matches artifacts |
| `/opsx:sync` | Merge delta specs into main specs |
| `/opsx:bulk-archive` | Archive multiple changes at once |
| `/opsx:onboard` | Guided tutorial through the complete workflow |

---

## Core Commands

### `/opsx:propose`

Create a new change and generate all planning artifacts in one step. The default start command.

```
/opsx:propose [change-name-or-description]
```

**What it creates:**

```
openspec/changes/<change-name>/
├── proposal.md
├── specs/
│   └── <area>/spec.md
├── design.md
└── tasks.md
```

**Example:**

```
/opsx:propose add-dark-mode
```

Creates the full change folder and all planning artifacts. Stops when ready for `/opsx:apply`.

**When to use:** Your default starting point. Use for most features.

---

### `/opsx:explore`

Think through ideas, investigate problems, and compare approaches before committing to a change.

```
/opsx:explore [topic]
```

**What it does:**
- Opens an exploratory conversation — no artifacts created
- Investigates the codebase to answer questions
- Compares options and approaches
- Can transition to `/opsx:propose` when insights crystallize

**Example:**

```
/opsx:explore how should we handle authentication for the mobile app?
```

**When to use:** Requirements are unclear, you need to compare approaches, or you want to investigate before writing a spec.

---

### `/opsx:apply`

Implement tasks from the change. Works through `tasks.md` one task at a time.

```
/opsx:apply [change-name]
```

**What it does:**
- Reads `tasks.md` and identifies incomplete tasks `[ ]`
- Works through tasks sequentially, writing code and creating files
- Marks tasks complete `[x]` as it goes
- Can resume from where it left off if interrupted

**When to use:** After `/opsx:propose` or `/opsx:ff` — always start implementation in a **clean context window**.

---

### `/opsx:archive`

Archive a completed change. Moves it to `openspec/changes/archive/YYYY-MM-DD-<name>/`.

```
/opsx:archive [change-name]
```

**What it does:**
- Checks artifact and task completion (warns if incomplete tasks remain)
- Offers to sync delta specs into `openspec/specs/` if not already done
- Moves the change folder to the archive

**Tip:** Run `/opsx:verify` before archiving to catch implementation gaps.

---

## Expanded Workflow Commands

### `/opsx:new`

Start a new change scaffold. Creates the folder and metadata only — no artifacts yet.

```
/opsx:new [change-name] [--schema <schema-name>]
```

**What it creates:**

```
openspec/changes/<change-name>/
└── .openspec.yaml    # schema, created date
```

**When to use:** When you want step-by-step artifact creation with review between each step. Pair with `/opsx:continue`.

---

### `/opsx:continue`

Create the next artifact in the dependency chain.

```
/opsx:continue [change-name]
```

**What it does:**
- Shows all artifacts and their status: done ✓, ready ◆, blocked ○
- Creates the next ready artifact
- Shows what becomes available after creation

**Example output:**

```
✓ proposal    (done)
◆ specs       (ready)
◆ design      (ready)
○ tasks       (blocked - needs: specs)
```

**When to use:** For complex changes where you want to review and edit each artifact before proceeding.

---

### `/opsx:ff`

Fast-forward through artifact creation. Creates all planning artifacts at once in dependency order.

```
/opsx:ff [change-name]
```

**When to use:** When you have a clear picture of what you're building and don't need to review artifacts one-by-one. Equivalent to running `/opsx:continue` until all artifacts are done.

---

### `/opsx:verify`

Validate that implementation matches your change artifacts across three dimensions.

```
/opsx:verify [change-name]
```

**Verification dimensions:**

| Dimension | What it checks |
|---|---|
| **Completeness** | All tasks done, all requirements implemented, scenarios covered |
| **Correctness** | Implementation matches spec intent, edge cases handled |
| **Coherence** | Design decisions reflected in code, patterns consistent |

**Severity levels:** CRITICAL (blocks), WARNING (note but proceed), SUGGESTION (optional)

**When to use:** Before `/opsx:archive` to catch spec drift and incomplete tasks.

---

### `/opsx:sync`

Merge delta specs from a change into the main `openspec/specs/` directory.

```
/opsx:sync [change-name]
```

**When to use:** Usually not needed — `/opsx:archive` offers to sync automatically. Use manually when running parallel changes that share the same base specs, or when you want to preview the merge before archiving.

---

### `/opsx:bulk-archive`

Archive multiple completed changes at once. Handles spec conflicts between changes.

```
/opsx:bulk-archive [change-names...]
```

**What it does:**
- Lists all completed changes
- Detects spec conflicts across changes
- Resolves conflicts by inspecting what's actually implemented in the codebase
- Archives in chronological order

**When to use:** After a parallel work sprint with multiple simultaneous changes.

---

### `/opsx:onboard`

Guided interactive tutorial through the complete workflow using your actual codebase.

```
/opsx:onboard
```

**What it does:** Walks through all 10 phases — explore → propose → spec → design → tasks → apply → verify → archive — on a real small improvement in your project. Takes 15–30 minutes.

**When to use:** First time using OpenSpec on a new project.

---

## Legacy Commands

Still functional but the `/opsx:*` commands are recommended.

| Command | What it does |
|---|---|
| `/openspec:proposal` | Create all artifacts at once (old all-or-nothing style) |
| `/openspec:apply` | Implement the change |
| `/openspec:archive` | Archive the change |

Existing projects using legacy commands can be continued with `/opsx:*` — the artifact structure is compatible.

---

## Artifact Structure

A fully planned change contains:

```
openspec/changes/<change-name>/
├── .openspec.yaml     # metadata (schema, created date)
├── proposal.md        # what and why
├── specs/
│   └── <area>/
│       └── spec.md    # requirements and scenarios
├── design.md          # how (architecture, decisions)
└── tasks.md           # implementation checklist
```

Archived changes move to:

```
openspec/changes/archive/YYYY-MM-DD-<change-name>/
```

---

## Decision Guide

| Situation | Command |
|---|---|
| Starting a new feature | `/opsx:propose` |
| Requirements unclear, need to think | `/opsx:explore` |
| Want step-by-step artifact control | `/opsx:new` → `/opsx:continue` |
| Clear picture, want everything fast | `/opsx:propose` or `/opsx:ff` |
| Implementing tasks | `/opsx:apply` |
| Verifying before archive | `/opsx:verify` |
| Completing a change | `/opsx:archive` |
| Multiple finished changes to close | `/opsx:bulk-archive` |
| New to OpenSpec | `/opsx:onboard` |

### When NOT to run the full pipeline

The full propose → apply → verify → archive pipeline is designed for features with meaningful design decisions. Skip or trim it for smaller work:

| Situation | Recommended approach |
|---|---|
| Bug fix (1–3 files) | `/opsx:explore` to diagnose → `/opsx:propose fix-<name>` with minimal tasks → `/opsx:apply` → `/opsx:archive` (skip `/opsx:verify`) |
| Tiny fix (1 function, obvious) | Just implement it. No OpenSpec needed. |
| Spike or experiment | `/opsx:explore` only. Don't create a change — you may throw it away. |
| Dependency bump with no API changes | Direct implementation. OpenSpec adds no value here. |
| Feature touching shared infrastructure | Run the full pipeline — `/opsx:verify` catches cross-module spec drift. |

**Rule of thumb:** If `tasks.md` would have only 1–2 tasks, the overhead of a full spec may not be worth it. Use `/opsx:explore` to confirm your approach, then implement directly. Reserve the full pipeline for changes where the design decisions are non-obvious or the implementation will take more than an hour.

---

## Troubleshooting

| Problem | Solution |
|---|---|
| "Change not found" | Specify name explicitly: `/opsx:apply add-dark-mode` |
| "No artifacts ready" | Run `openspec status --change <name>` to see what's blocking |
| Commands not recognized | Run `openspec init` then `openspec update`, restart AI tool |
| Artifacts incomplete or wrong | Add project context in `openspec/config.yaml`, use `/opsx:continue` for more control |
