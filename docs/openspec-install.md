# OpenSpec Installation Guide

Setting up OpenSpec for use with OpenCode and the extended workflow profile.

---

## Requirements

- Node.js 20.19.0 or higher
- An existing project directory

---

## 1. Install OpenSpec

```bash
npm install -g @fission-ai/openspec@latest
```

Verify:

```bash
openspec --version
```

---

## 2. Initialize in Your Project

Navigate to your project root and run:

```bash
cd your-project
openspec init
```

This creates the `openspec/` directory structure:

```
openspec/
├── specs/              # Source of truth — your system's current behavior
│   └── <domain>/
│       └── spec.md
├── changes/            # One folder per in-progress change
│   └── <change-name>/
│       ├── proposal.md
│       ├── design.md
│       ├── tasks.md
│       └── specs/      # Delta specs (what this change adds/modifies/removes)
│           └── <domain>/
│               └── spec.md
└── config.yaml         # Optional project configuration
```

### Brownfield projects: seed config.yaml first

OpenSpec has no built-in codebase scanner. On an existing project, the AI will generate generic proposals unless you tell it about your architecture. Add a `context` block to `openspec/config.yaml` before running any `/opsx:*` commands:

```yaml
# openspec/config.yaml
context: |
  TypeScript project with strict mode. Input validation uses Zod.
  API handlers follow the repository pattern — no direct DB access in routes.
  Auth is JWT-based via src/middleware/auth.ts.
  All DB access goes through src/db/repositories/.
  Tests use Vitest. Every new module needs a corresponding test file.
  State management uses Zustand. Styling uses Tailwind CSS.
```

Also run `/repo-overview`, `/repo-auth`, `/repo-routes`, and `/repo-models` before your first `/opsx:propose` to give the agent codebase context. Save key findings with `/mem-save`.

---

## 3. Enable the Extended Workflow Profile

The default profile (`core`) only includes `/opsx:propose`, `/opsx:explore`, `/opsx:apply`, and `/opsx:archive`. To get the full command set including `/opsx:new`, `/opsx:continue`, `/opsx:ff`, `/opsx:verify`, `/opsx:sync`, `/opsx:bulk-archive`, and `/opsx:onboard`:

```bash
openspec config profile
```

Select **workflows** when prompted.

Then apply it:

```bash
openspec update
```

This regenerates the slash-command skill files in `.claude/skills/` with the expanded command set. These OpenSpec-managed files are distinct from the manual OpenCode review skills installed under `~/.config/opencode/skills/`.

---

## 4. Verify Commands Are Available in OpenCode

After `openspec update`, the `/opsx:*` commands are available in OpenCode through those generated slash-command files. Restart OpenCode if it was already running, then type `/opsx:` — you should see the full command list including the expanded commands.

---

## 5. First Run

### New projects

Run the interactive tutorial:

```
/opsx:onboard
```

This walks through the complete workflow (explore → propose → spec → design → tasks → apply → verify → archive) using a real small improvement from your actual codebase. Takes 15–30 minutes.

Or jump straight in:

```
/opsx:propose your feature description here
```

### Existing (brownfield) projects

Don't start with `/opsx:propose` on an unfamiliar codebase — the spec will be generic. Run these first in OpenCode:

```
/repo-overview
/repo-auth
/repo-routes
/repo-models
/mem-save <key findings about the architecture>
```

Then seed `openspec/config.yaml` with what you learned (see step 2), and only then run `/opsx:propose`.

### Bug fixes

Skip the full pipeline. Use explore to diagnose first:

```
/opsx:explore <describe the bug and symptoms>
```

Once you understand the cause, create a minimal change:

```
/opsx:propose fix-<name>
```

The proposal and tasks will be short — that's fine. Don't force the full spec/design cycle on a one-file fix.

---

## Keeping OpenSpec Updated

When a new version releases, upgrade the package and regenerate skills in each project:

```bash
# Upgrade globally
npm install -g @fission-ai/openspec@latest

# Regenerate skills in each project
cd your-project
openspec update
```

---

## Useful CLI Commands

```bash
openspec list                      # List all active changes
openspec show <change-name>        # View change details and artifact status
openspec validate <change-name>    # Validate spec formatting
openspec view                      # Open interactive dashboard
openspec status --change <name>    # Show artifact dependency status
openspec schemas                   # List available workflow schemas
```

---

## Telemetry

OpenSpec collects anonymous usage stats (command names and version only — no arguments, paths, or content). Opt out if preferred:

```bash
export OPENSPEC_TELEMETRY=0
```

---

## Reference

- [OpenSpec commands reference](./openspec-commands.md) — full `/opsx:*` command reference
- [Workflow examples](./opencode-workflows.md) — end-to-end workflows combining OpenCode + OpenSpec
- [OpenSpec GitHub](https://github.com/Fission-AI/OpenSpec)
