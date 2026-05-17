# === Kipi: Project Rules (managed) — start ===
# Managed by Kipi (`/kipi-install`). Edit by re-running install or by toggling the
# block manually between the start/end markers. Content outside the markers is
# preserved on every Kipi run.

## Knowledge Verification Protocol (CRITICAL)

**Core-Knowledge is a STARTING POINT, not an AUTHORITATIVE SOURCE.**

When using knowledge files:
1. **Treat as orientation** — Use knowledge docs to understand codebase structure, key concepts, and where to look.
2. **Always verify specifics** — Before making claims or changes based on knowledge docs, verify details in the actual code.
3. **Knowledge may be stale** — Code evolves faster than documentation; trust the code when they conflict.
4. **Ask when uncertain** — If knowledge docs don't provide enough detail, ASK the user or INVESTIGATE the codebase.
5. **Don't guess** — Never fill in gaps by assuming; verify or ask.

### Verification Workflow

```
Knowledge Doc → Understand Structure → Find Relevant Code → Verify Details → Act
```

---

## Base Knowledge Protocol

Before responding to any user request:

1. **Check for Core-Knowledge:**
   - Look for `knowledge/core_knowledge/index.md` in the project root.
   - If it exists, scan the index (especially the **Folder Contracts** section) to understand the available knowledge.
   - Load only the relevant knowledge files based on the user's request.
   - **Use knowledge as a MAP to navigate to actual code.**

2. **Context Loading:**
   - DO NOT load all knowledge files — only what's relevant.
   - If unsure which knowledge applies, ask the user.
   - Reference knowledge naturally in responses when applicable.
   - **Always verify specifics in code before acting.**

---

## Kipi Handshake Protocol

**CRITICAL:** This rule applies to ALL agents in this project — **EXCEPT Kipi itself**.

### When NOT to Trigger

Skip the handshake entirely if:
- The current agent IS Kipi (Core-Knowledge Keeper).
- You are executing a Kipi skill/workflow (e.g. `/kipi-update-core`).
- The user is already in a Core-Knowledge update session.

Kipi manages Core-Knowledge directly — it doesn't need to suggest calling itself.

### When to Trigger

After completing any of these actions (and you are NOT Kipi):
- Making changes to source code files.
- Creating new files in the repository.
- Receiving new project-level information from the user.
- Completing a significant task that affects project structure.

### What to Do

Append this message at the end of your response:

> **Knowledge Update Available**
> Project knowledge may need updating based on these changes.
> → Run `/kipi-update-core` to sync Core-Knowledge with Kipi.

### Why This Matters

- Keeps Core-Knowledge synchronized with actual code.
- Ensures documentation doesn't drift from implementation.
- Maintains a single source of truth for project knowledge.
- Enables developers to always have accurate context.

---

## Kipi Skill Reference

### `/kipi-update-core`
**Purpose:** Trigger Kipi to analyze recent changes and update Core-Knowledge.
**When:** after code changes, after receiving new project info, when documentation may be outdated, periodically for maintenance.
**What it does:**
1. Analyzes recent changes or provided information.
2. Determines which Core-Knowledge files are affected.
3. Proposes specific updates with rationale.
4. Waits for user approval before making changes.
5. Updates Core-Knowledge index if structure changed.

### `/kipi-developer-interview`
**Purpose:** Run 10 targeted questions to fill gaps in `core_knowledge/`.
**When:** after initial structure exists but content is sparse, after onboarding, periodically to refresh stale areas.
**What it does:** analyses gaps, generates questions, accepts chat or transcript answers, distills (with name + private_knowledge self-checks) into per-file proposals, routes identity content into `team_and_stakeholders/`. Verbatim answers stay in a sidecar log.

### `/kipi-cleanup-knowledge`
**Purpose:** Audit Core-Knowledge for structure drift, Hard Rules violations, hygiene issues, and legacy `_about.md` migration.
**When:** when Kipi flags drift on activation, after major code changes, periodically.
**What it does:** runs three audits (Structure, Hard Rules, Hygiene), surfaces findings with proposed actions, executes only user-approved actions, generates a cleanup report.

### `/kipi-install`
**Purpose:** Install or refresh Kipi for the IDE(s) configured in BMad.
**When:** first activation in a new IDE, after upgrading Kipi, when adding a new IDE.
**What it does:** reads BMad's IDE config, copies the right adapter bundle into the IDE's skill / rules / slash-command locations, caches the resolution at `_bmad/kipi/ide-resolved.yaml`. Idempotent.

---

## Folder Awareness

### Key Directories

| Folder | Purpose | Kipi Interaction |
|--------|---------|------------------|
| `knowledge/core_knowledge/` | Structured knowledge base (shared, pushed to repo) | Kipi writes here |
| `knowledge/private_knowledge/` | Private notes & raw inputs (git-ignored, local only) | User & Kipi raw-data writes here |
| `user-data/` | Raw input files (legacy entry point) | Kipi monitors this |
| `ready-for-classify/` | Processed files | Kipi writes here |
| `_archive/` | Original files | Kipi archives here |

### Required Directory Structure

Kipi enforces this structure on every initialized repo:

```
project-root/
└── knowledge/
    ├── core_knowledge/
    │   ├── index.md                       (routing table + Folder Contracts — single source of truth)
    │   ├── architecture.md                (system design)
    │   ├── components/
    │   ├── infrastructure/
    │   ├── processes/
    │   ├── team_and_stakeholders/         (the only place names live)
    │   └── <optional folders if approved>
    └── private_knowledge/                 (git-ignored, never referenced from core_knowledge)
        ├── raw_data/
        │   ├── emails_team_chats/
        │   ├── screenshots/
        │   └── meetings_transcripts/
        └── personal_tasks/
```

> **Folder contracts** live in a dedicated section inside `core_knowledge/index.md` — read it before adding new content. (Older Kipi versions used per-folder `_about.md` files; those are migrated by `[CL] Cleanup Knowledge`.)

### Hard Rules (enforced across all writing skills)

1. **Names of people** appear ONLY in `core_knowledge/team_and_stakeholders/`. All other Core-Knowledge files reference roles (e.g. "the platform team lead") and link there.
2. **`private_knowledge/`** is NEVER referenced (linked, cited, or path-mentioned) from any file in `core_knowledge/`.
3. **`private_knowledge/`** content is NEVER committed to git. Only `.gitkeep` files preserve the structure.

### Protected Paths

Kipi only writes to these locations:
- `knowledge/core_knowledge/`
- `knowledge/private_knowledge/` (raw inputs only; never committed)
- `ready-for-classify/`
- `_bmad/_memory/kipi-sidecar/`
- IDE adapter targets configured in `_bmad/kipi/ide-resolved.yaml` (rules file + skill stubs + slash commands for the detected IDE)

---

## Integration with Other Agents

Any BMad agent in this project should:

1. **On Activation:** Check `knowledge/core_knowledge/index.md` for relevant context.
2. **During Work:** Reference Core-Knowledge as a STARTING POINT, then verify specifics in actual code.
3. **After Changes:** Trigger the Kipi Handshake (suggest `/kipi-update-core`) — **unless you ARE Kipi**.
4. **Always Remember:** Knowledge docs are a MAP, not the territory — code is the source of truth.

**Note:** Kipi is exempt from the handshake protocol since it manages Core-Knowledge directly.

# === Kipi: Project Rules (managed) — end ===
