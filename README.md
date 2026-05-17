# Kipi — Core-Knowledge Keeper

A BMad module that manages repository documentation, systematic knowledge organization, and intelligent file processing.

Kipi scans your codebase, builds deep documentation organized into chapters, processes files you drop into a folder, and keeps everything in sync as your code evolves.

---

## Workshop

For guided install (Node.js → BMad → Kipi) and a custom-agent building exercise, start at **[START_HERE.md](START_HERE.md)** and use the files in `workshop/`.

---

## Prerequisites

- **BMad (latest version)** must be installed in the target project
- A supported IDE (Cursor, Claude Code, etc.) — Kipi reads your choice from BMad's config

If BMad is not installed, install it first. Kipi will refuse to activate without it.

---

## Installation

### Step 1: Copy agent files (brain)

Copy everything under `agent_files/` into your project's `_bmad/kipi/`:

```bash
mkdir -p <your-project>/_bmad/kipi
cp -R agent_files/. <your-project>/_bmad/kipi/
```

This includes the main `SKILL.md`, sub-skills (`kipi-*`), workflows, templates, `ide_adapters/` (per-IDE install bundles), and `rules-template.md`.

### Step 2: Activate Kipi (IDE stubs + rules)

In your IDE, run `/kipi` (or open Kipi from the skill menu).

On first activation, **kipi-install** runs automatically. It reads which IDE(s) BMad configured and, for each one:

| IDE | Skill stubs go to | Rules block merged into |
|-----|-------------------|-------------------------|
| Cursor | `.cursor/skills/` | `.cursorrules` |
| Claude Code | `.claude/skills/` | `CLAUDE.md` |
| VS Code Copilot | — | `.github/copilot-instructions.md` |
| Windsurf | — | `.windsurfrules` |
| Cline / Roo | — | `AGENTS.md` |

The rules **content** is the same everywhere — it lives in `agent_files/rules-template.md` and is copied into whichever file your IDE uses.

To re-run or add another IDE later, use **[IN] Install** from Kipi's menu.

### Step 3: Register with BMad (if needed)

Tell `bmad-help`: "I installed the Kipi module, help me register it."

Or ensure `_bmad/config.yaml` lists Kipi and `{user_name}` is set.

---

## Usage

### Activate Kipi

In your IDE, invoke Kipi — say "talk to Kipi" or use `/kipi` (or the equivalent slash command your IDE exposes after install).

Kipi will check that BMad is installed, load your config, and present a menu:

| Code | What It Does |
|------|-------------|
| RI | Initialize knowledge structure for your repo |
| CS | Deep-scan codebase and write documentation |
| UD | Process files dropped in `user-data/` |
| UK | Update knowledge after code changes |
| DI | Run a 10-question developer interview to fill knowledge gaps |
| CL | Cleanup and maintenance (incl. structure-drift audit) |
| ST | Show knowledge status and coverage |

### First Time Setup

1. Activate Kipi → select **[RI] Repository Initialize**
2. Kipi scans your repo and walks you through a gated proposal:
   - **Tier-1 baseline** (always created): routing index, architecture, components, infrastructure, processes, team_and_stakeholders
   - **Monorepo layout** (if detected): you choose single / per-project / hybrid
   - **Tier-2 candidates** (one-by-one approval): operations, integrations, api, data_models, events, security, ml, cli — only when relevant
3. Kipi creates the approved structure plus the private-knowledge tree and configures `.gitignore` automatically
4. Then run **[CS] Code Scan** to populate content

### Knowledge Directories

```
knowledge/
├── core_knowledge/                  ← shared, pushed to repo
│   ├── index.md                     (routing table — read this first)
│   ├── architecture.md              (system design)
│   ├── components/
│   │   └── _about.md                (folder contract: what goes here)
│   ├── infrastructure/
│   │   └── _about.md
│   ├── processes/
│   │   └── _about.md
│   ├── team_and_stakeholders/       (THE only place names live)
│   │   ├── _about.md
│   │   ├── contacts.md
│   │   ├── team-structure.md
│   │   └── ownership-map.md
│   └── <tier-2 folders if approved>
│
└── private_knowledge/               ← git-ignored, never pushed
    ├── raw_data/
    │   ├── emails_team_chats/
    │   ├── screenshots/
    │   └── meetings_transcripts/
    └── personal_tasks/
```

#### Hard rules enforced by Kipi

1. **Names of people** appear ONLY in `team_and_stakeholders/`. All other Core-Knowledge files reference roles.
2. **`private_knowledge/`** is NEVER referenced (linked, cited, or path-mentioned) from any file in `core_knowledge/`.
3. **`private_knowledge/`** content is NEVER committed to git. Only `.gitkeep` files preserve the structure.

#### .gitignore (managed automatically)

Kipi creates or appends to your project's `.gitignore` during `[RI] Repository Initialize`:

```
# === Kipi: Private Knowledge (never commit content) ===
knowledge/private_knowledge/**
!knowledge/private_knowledge/
!knowledge/private_knowledge/**/
!knowledge/private_knowledge/**/.gitkeep

# === Kipi: Sidecar workflow state (not for VCS) ===
_bmad/_memory/kipi-sidecar/workflow-state.json
```

### Processing User Files

Drop any file into `user-data/` (transcripts, notes, logs, etc.), then run **[UD]**. Kipi transforms it into a structured document in `ready-for-classify/` and archives the original.

### Keeping Knowledge Current

After code changes, run `/kipi-update-core` or other agents will remind you via the Kipi Handshake Protocol.

---

## File Structure (This Repo)

```
kipi-agent/
├── agent_files/               # → copy to _bmad/kipi/ (canonical brain)
│   ├── SKILL.md
│   ├── rules-template.md      # IDE-agnostic rules (merged by kipi-install)
│   ├── ide_adapters/          # per-IDE skill stubs + commands
│   ├── kipi-install/
│   ├── kipi-*/
│   ├── workflows/
│   └── ...
├── workshop/                  # participant prompts (install + agent builder)
├── START_HERE.md
├── LICENSE
└── README.md
```
