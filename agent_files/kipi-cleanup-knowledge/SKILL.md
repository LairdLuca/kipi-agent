---
name: kipi-cleanup-knowledge
description: "Run maintenance and cleanup on Core-Knowledge. Use when Kipi selects [CL] or user says 'cleanup knowledge' or 'maintenance'."
---

# Kipi — Cleanup Knowledge

## Overview

Run maintenance and cleanup on the Core-Knowledge base to ensure structural integrity, enforce the directory contract, audit for Hard Rule violations, remove orphaned files, fix broken references, migrate legacy structures, and keep the index accurate.

This skill runs in three audit passes (Structure, Rules, Hygiene), then proposes specific actions. **Kipi never auto-modifies — every action requires explicit user approval.**

## On Activation

Execute the following process:

### Step 1: Structure Audit (drift detection)

Compare the current `knowledge/` tree against the canonical contract:

**Required tree:**

```
knowledge/
├── core_knowledge/
│   ├── index.md                          (must contain a "Folder Contracts" section)
│   ├── architecture.md
│   ├── components/                       (folder; .gitkeep allowed)
│   ├── infrastructure/                   (folder; .gitkeep allowed)
│   ├── processes/                        (folder; .gitkeep allowed)
│   └── team_and_stakeholders/
│       ├── contacts.md
│       ├── team-structure.md
│       └── ownership-map.md
└── private_knowledge/
    ├── raw_data/
    │   ├── emails_team_chats/.gitkeep
    │   ├── screenshots/.gitkeep
    │   └── meetings_transcripts/.gitkeep
    └── personal_tasks/.gitkeep
```

Plus at project root:
- `.gitignore` containing the `# === Kipi: Private Knowledge` block

For each item, classify:
- ✅ **Present** — exists, no action
- ⚠️ **Missing** — required item is absent
- 🔄 **Misnamed** — looks like the legacy `personal_knowledge/` or otherwise non-canonical
- 📦 **Legacy** — was correct in an older Kipi version but is no longer canonical (e.g. `_about.md` files; see Step 1b)
- ❓ **Extra** — folder/file outside the contract (optional folders may be legitimate; check `index.md` Folder Contracts section)
- 🚫 **Wrong-shape** — e.g. `team_and_stakeholders.md` as a file when it should be a folder

Common drift patterns to detect:
- `knowledge/personal_knowledge/` exists (legacy name) → propose rename + restructure to `private_knowledge/`
- `core_knowledge/overview.md` exists (legacy chapter) → propose folding mission into `index.md`
- Baseline folders missing → propose creation (with `.gitkeep`)
- `team_and_stakeholders/` missing one of the 3 required files → propose creation
- `private_knowledge/` tree incomplete → propose creating missing sub-folders + `.gitkeep`s
- `.gitignore` missing the Kipi block → propose append (idempotent)
- `index.md` doesn't follow routing-table format or is missing the **Folder Contracts** section → propose regeneration from `index-template.md`

### Step 1b: Legacy `_about.md` migration check

Older Kipi versions created an `_about.md` file inside every folder. The current canonical pattern keeps folder contracts in a single **Folder Contracts** section inside `index.md`.

For each `_about.md` file found in `core_knowledge/`:
1. Read its content.
2. Stage a migration action that will:
   - Locate the matching folder block in `index.md`'s Folder Contracts section (or create one if absent).
   - Merge any user-customised content from `_about.md` into that block (preserve scope statements and "what does NOT go here" lists; deduplicate against the canonical content).
   - Delete the `_about.md` file after the merge is verified.
3. Surface in the audit as: `📦 Legacy: components/_about.md → migrate into index.md Folder Contracts`.

If `index.md` itself has no Folder Contracts section, propose regenerating `index.md` from `index-template.md` first, then performing the per-folder merge.

### Step 2: Hard Rules Audit

Scan every file in `core_knowledge/` for Hard Rule violations:

**Rule 1 — Names outside `team_and_stakeholders/`:**
- For each `core_knowledge/` file NOT inside `team_and_stakeholders/`:
  - Use heuristics to flag potential personal names (Capitalized FirstName + LastName patterns, "@username" handles, common name lists)
  - Flag each occurrence with file path + line number + suspected name

**Rule 2 — `private_knowledge/` references:**
- For each `core_knowledge/` file:
  - Search for the literal string `private_knowledge`
  - Search for paths starting with `knowledge/private_knowledge/`
  - Flag each occurrence with file path + line number

**Rule 3 — Committed content in `private_knowledge/`:**
- Walk `knowledge/private_knowledge/` and check `git ls-files`
- Flag any tracked files other than `.gitkeep`

### Step 3: Hygiene Audit

Standard cleanup checks:
- Orphaned files (exist on disk but not referenced in `index.md` routing table)
- Broken references in `index.md` (point to non-existent files)
- Coverage table accuracy (matches actual file states)
- File-size signals:
  - Any file >500 lines → suggest split / promote-to-folder
  - Any file >1500 lines → warn
  - Folders with only 1 file → suggest demote-to-root-file
  - `index.md` glossary >30 entries → suggest promote to standalone `glossary.md`
- `_template.md` opportunities: any folder with 3+ similar-shaped files but no `_template.md` → suggest copying the matching scaffold from `_bmad/kipi/templates/folder-templates/`

### Step 4: Present Findings

Show all findings organized by category and severity:

```
🔍 **Cleanup Audit Report**

## A. Structure Drift  (severity: critical)
{{drift_findings_with_proposed_actions}}

## A.1 Legacy migrations  (severity: critical)
{{legacy_about_md_migrations}}

## B. Hard Rule Violations  (severity: critical)
{{rule_violations_with_locations}}

## C. Hygiene Issues  (severity: warning / info)
{{orphans, broken_links, size_signals, _template.md suggestions}}

## D. Healthy Items
{{green-checked items, brief}}

---

## Proposed Actions ({{n}} total)

For each, you can [Y]es approve, [N]o skip, or [E]xplain.

[1] {{action_with_specifics}}
[2] {{action_with_specifics}}
...

[A] Approve all critical (structure + rules + legacy migrations)
[H] Approve all hygiene only
[N] No actions; just report
```

STOP and WAIT for user response.

### Step 5: Execute Cleanup

For each user-approved action:
1. State exactly what will change before acting
2. Apply the change (reuse templates from `_bmad/kipi/templates/` where applicable)
3. Re-verify the rule that triggered the action
4. Report success/failure for that action

**For Structure Drift actions specifically:**
- Renaming `personal_knowledge/` → `private_knowledge/`: preserve content, restructure into the canonical sub-tree, add `.gitkeep`s, update `.gitignore` (idempotent append)
- Creating missing baseline folders: create the folder with a `.gitkeep`. (Do NOT create `_about.md` — folder contracts live in `index.md`.)
- Creating missing `team_and_stakeholders/` files: use `_bmad/kipi/templates/team-and-stakeholders/*.md`
- Regenerating `index.md`: use `_bmad/kipi/templates/index-template.md`, preserving existing glossary entries and coverage statuses where possible. Always include the **Folder Contracts** section.

**For Legacy `_about.md` migration actions:**
1. Ensure `index.md` has a **Folder Contracts** section. If missing, regenerate `index.md` first (preserving glossary, coverage, mission).
2. For each `_about.md`:
   a. Read the file.
   b. Identify the matching folder block in the Folder Contracts section.
   c. Merge user-customised content (scope additions, narrowed/expanded "does NOT hold" entries) into the block. Preserve canonical text; append unique user content.
   d. Show a per-file diff to the user; ask [A]ccept / [E]dit / [S]kip.
   e. On accept: write the updated `index.md`, delete the `_about.md`.
3. After all files processed: regenerate `index.md`'s Routing Table and Coverage rows so they reference the right folders.

**For Hard Rule violation actions:**
- Names outside `team_and_stakeholders/`: present each occurrence; ask user whether to (a) rewrite as a role, (b) move identity to `team_and_stakeholders/` and link, or (c) leave it (false positive)
- `private_knowledge/` references: present each occurrence; ask user whether to (a) remove the reference entirely, (b) replace with a self-contained equivalent in `core_knowledge/`, or (c) leave it (false positive — e.g. inside this very rules block)

### Step 6: Report

Generate cleanup report with:
- Total findings by category
- Actions taken
- Actions skipped
- Files updated
- Legacy `_about.md` files migrated and deleted (count + list)
- Updated `index.md` if structure changed
- Save report to `_bmad/_memory/kipi-sidecar/cleanup-reports/{date}.md`

Return to Kipi menu.

## Hard Rules (inherited from Kipi persona)

This skill enforces the Hard Rules; it does not violate them. See `_bmad/kipi/SKILL.md` for the full definition.

1. **Names of people** appear ONLY in `core_knowledge/team_and_stakeholders/`. This skill scans for and proposes fixes to violations.
2. **No `private_knowledge/` references** in `core_knowledge/`. This skill scans for and proposes fixes to violations.
3. **Self-check before writing** — before persisting any cleanup action's output to `core_knowledge/`, run the same name + path scan.

## Success Criteria

- Every drift item is detected and presented
- Every legacy `_about.md` file is detected and offered for migration
- Every Hard Rule violation is detected and presented
- Every action is explicitly approved before execution
- `index.md` reflects actual file structure after cleanup, including a populated **Folder Contracts** section
- No `_about.md` files remain after migration is approved
- Cleanup report generated with all actions and skipped items
- No drift left unreported

## Resource Reference

| Resource | Path |
|---|---|
| Index template | `_bmad/kipi/templates/index-template.md` |
| Architecture template | `_bmad/kipi/templates/architecture-template.md` |
| Folder authoring scaffolds | `_bmad/kipi/templates/folder-templates/` |
| Team-and-stakeholders templates folder | `_bmad/kipi/templates/team-and-stakeholders/` |
| Repository init checklist | `_bmad/kipi/checklists/repository-initialize-checklist.md` (canonical structure reference) |
