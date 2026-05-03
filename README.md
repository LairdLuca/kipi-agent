# Kipi — Core-Knowledge Keeper

A BMad module that manages repository documentation, systematic knowledge organization, and intelligent file processing.

Kipi scans your codebase, builds deep documentation organized into chapters, processes files you drop into a folder, and keeps everything in sync as your code evolves.

---

## Prerequisites

- **BMad (latest version)** must be installed in the target project
- **Cursor IDE** with skills support

If BMad is not installed, visit the BMad repository and install it first. Kipi will refuse to activate without it.

---

## Installation

### Step 1: Copy Agent Files

Copy the `agent_files/` folder contents into your project's `_bmad/kipi/` directory:

```bash
cp -r agent_files/ <your-project>/_bmad/kipi/
```

This includes:
- `SKILL.md` — Main Kipi agent definition
- `bmad-skill-manifest.yaml` — Module manifest
- `kipi-*/` — Sub-skill definitions (6 sub-skills)
- `workflows/` — Workflow step files
- `checklists/` — Validation checklists
- `templates/` — Output templates
- `schemas/` — State tracking schema
- `data/` — Classification rules and batching config

### Step 2: Copy Cursor Skills

Copy each folder from `cursor-skills/` into your project's `.cursor/skills/`:

```bash
cp -r cursor-skills/kipi/ <your-project>/.cursor/skills/kipi/
cp -r cursor-skills/kipi-cleanup-knowledge/ <your-project>/.cursor/skills/kipi-cleanup-knowledge/
cp -r cursor-skills/kipi-code-scan-document/ <your-project>/.cursor/skills/kipi-code-scan-document/
cp -r cursor-skills/kipi-developer-interview/ <your-project>/.cursor/skills/kipi-developer-interview/
cp -r cursor-skills/kipi-knowledge-status/ <your-project>/.cursor/skills/kipi-knowledge-status/
cp -r cursor-skills/kipi-process-user-data/ <your-project>/.cursor/skills/kipi-process-user-data/
cp -r cursor-skills/kipi-repository-initialize/ <your-project>/.cursor/skills/kipi-repository-initialize/
cp -r cursor-skills/kipi-update-core/ <your-project>/.cursor/skills/kipi-update-core/
```

### Step 3: Add Cursor Rules

Merge the contents of `cursorrules-template` into your project's `.cursorrules` file. This adds:
- Knowledge Verification Protocol
- Base Knowledge Protocol (tells agents to check knowledge before responding)
- Kipi Handshake Protocol (tells agents to suggest `/kipi-update-core` after changes)
- Folder Awareness

> **Note:** This is a manual merge — review the template and add what's relevant to your existing `.cursorrules`.

### Step 4: Register with BMad

Tell `bmad-help`: "I installed the Kipi module, help me register it."

Or manually: ensure `_bmad/config.yaml` includes Kipi in the agents/modules section and that `{user_name}` is set (Kipi uses it for greetings).

---

## Usage

### Activate Kipi

In Cursor, invoke the Kipi skill — say "talk to Kipi" or use `/kipi`.

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
kipi/                          # repo root
├── agent_files/               # → copy to _bmad/kipi/
│   ├── SKILL.md
│   ├── bmad-skill-manifest.yaml
│   ├── kipi-cleanup-knowledge/
│   ├── kipi-code-scan-document/
│   ├── kipi-developer-interview/
│   ├── kipi-knowledge-status/
│   ├── kipi-process-user-data/
│   ├── kipi-repository-initialize/
│   ├── checklists/
│   ├── data/
│   ├── schemas/
│   ├── templates/
│   └── workflows/
├── cursor-skills/             # → copy to .cursor/skills/
│   ├── kipi/
│   ├── kipi-cleanup-knowledge/
│   ├── kipi-code-scan-document/
│   ├── kipi-developer-interview/
│   ├── kipi-knowledge-status/
│   ├── kipi-process-user-data/
│   ├── kipi-repository-initialize/
│   └── kipi-update-core/
├── cursorrules-template       # → merge into .cursorrules
├── LICENSE
└── README.md
```
