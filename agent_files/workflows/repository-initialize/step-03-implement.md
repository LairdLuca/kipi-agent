# Step 3: Implement Structure

## MANDATORY EXECUTION RULES (READ FIRST)

- 🛑 NEVER create folders/files outside the structure approved in Step 2
- ✅ ALWAYS use the index-template.md and other templates from `{agent-root}/templates/`
- 📋 SHOW the user the full creation preview before writing anything
- 🔄 UPDATE workflow-state.json after each file created
- 🧹 PURGE detailed proposal data from context after completion; keep only the summary
- 🔒 Treat `private_knowledge/` content as never-to-commit. Only `.gitkeep` files preserve folder structure.
- ➕ When `.gitignore` already exists at project root: APPEND idempotently with a header comment, log the action in the completion report. Never overwrite.
- 🚫 Do NOT create per-folder `_about.md` files. Folder contracts live inside `index.md` (single source of truth).

---

## Prerequisites

- Step 2 (Propose) must be completed
- workflow-state.json must show `structure_proposal.user_approved = true`

---

## Your Task

Create the approved Core-Knowledge structure: 2 root files (`index.md` with embedded folder contracts, `architecture.md`), the approved folders (each empty with a `.gitkeep`), the 3 mandatory `team_and_stakeholders/` files, the full `private_knowledge/` tree (with `.gitkeep` files), and the `.gitignore` rules at project root.

---

## Templates Inventory

| Template | Used for |
|---|---|
| `{agent-root}/templates/index-template.md` | Root `index.md` (includes the **Folder Contracts** section — populate from the catalog row of every approved folder) |
| `{agent-root}/templates/architecture-template.md` | Root `architecture.md` |
| `{agent-root}/templates/team-and-stakeholders/contacts-template.md` | `team_and_stakeholders/contacts.md` |
| `{agent-root}/templates/team-and-stakeholders/team-structure-template.md` | `team_and_stakeholders/team-structure.md` |
| `{agent-root}/templates/team-and-stakeholders/ownership-map-template.md` | `team_and_stakeholders/ownership-map.md` |

`{agent-root}/templates/folder-templates/` contains optional **authoring scaffolds** (component, infrastructure-topic, process, job). These are **not auto-installed**. Mention them in the completion report so users know the scaffolds exist when they want to start writing files.

---

## Execution Sequence

### 1. Verify Approval

Check workflow-state.json:
- `structure_proposal.user_approved` must be `true`
- If not: return to Step 2

### 2. Build Creation Manifest

Construct the full list of files & folders to create from `findings.structure_proposal`:
- Always: `index.md`, `architecture.md`, `components/`, `infrastructure/`, `processes/`, `team_and_stakeholders/` (with 3 files)
- Each entry in `approved_folders` that's not in the baseline → create as empty folder with `.gitkeep`
- `events_placement = "nested"` → put `events/` under `data_models/events/.gitkeep` instead
- Monorepo layout:
  - `single` → no projects subtree
  - `per-project` → for each project, replicate the baseline tree under `core_knowledge/<project_name>/`
  - `hybrid` → keep top-level shared, add `core_knowledge/projects/<project_name>/{components,infrastructure,processes}/.gitkeep` for each project

### 3. Show Creation Preview

```
📝 **Creation Preview**

Will create the following in `{{project_root}}`:

knowledge/
├── core_knowledge/
│   ├── index.md                                         (from index-template.md)
│   ├── architecture.md                                  (from architecture-template.md)
│   ├── components/
│   │   └── .gitkeep
│   ├── infrastructure/
│   │   └── .gitkeep
│   ├── processes/
│   │   └── .gitkeep
│   ├── team_and_stakeholders/
│   │   ├── contacts.md                                  (placeholder)
│   │   ├── team-structure.md                            (placeholder)
│   │   └── ownership-map.md                             (placeholder)
{{#each approved_optional_folders}}
│   ├── {{name}}/
│   │   └── .gitkeep
{{/each}}
{{#if monorepo_hybrid}}
│   └── projects/
{{#each monorepo_projects}}
│       └── {{project_name}}/
│           ├── components/.gitkeep
│           ├── infrastructure/.gitkeep
│           └── processes/.gitkeep
{{/each}}
{{/if}}
└── private_knowledge/
    ├── raw_data/
    │   ├── emails_team_chats/.gitkeep
    │   ├── screenshots/.gitkeep
    │   └── meetings_transcripts/.gitkeep
    └── personal_tasks/.gitkeep

Plus at project root:
- `.gitignore` (created or appended) with `private_knowledge/` block

Total: {{folder_count}} folders, {{file_count}} files

[P] Proceed with creation
[C] Cancel and return to menu
```

Wait for [P] or [C].

### 4. Create core_knowledge baseline

```
1. Create `{project-root}/knowledge/core_knowledge/`
2. Write `index.md` from index-template.md, substituting:
   - {{project_name}}
   - {{repository_type}}
   - {{primary_language}}
   - {{date}}
   - {{project_mission_1_paragraph}}  ← if not yet known, leave a TODO marker
   - In the **Folder Contracts** section, render one block per approved folder using the catalog row from step-02 (scope + does-NOT-hold). For optional folders, populate the "Optional folders enabled in this repo" subsection.
   - The optional_folders_present block (used by Routing Table, Coverage, and Folder Contracts) iterates over the approved optional folders.
3. Write `architecture.md` from architecture-template.md
4. For each baseline folder (`components/`, `infrastructure/`, `processes/`, `team_and_stakeholders/`):
   a. Create folder
   b. (No `_about.md`. Contracts live in `index.md`.)
5. Inside `team_and_stakeholders/`:
   - Write `contacts.md` from contacts-template.md
   - Write `team-structure.md` from team-structure-template.md
   - Write `ownership-map.md` from ownership-map-template.md
6. Add `.gitkeep` to `components/`, `infrastructure/`, `processes/` (so the empty folders survive git).
7. Report each file created:
   "✓ Created knowledge/core_knowledge/index.md"
   "✓ Created knowledge/core_knowledge/architecture.md"
   "✓ Created knowledge/core_knowledge/components/.gitkeep"
   ...
```

### 5. Create optional folders (per approval)

For each approved optional folder:

```
a. Create `{project-root}/knowledge/core_knowledge/{folder}/`
b. Write `.gitkeep` inside (no `_about.md` — its contract lives in `index.md`).
c. Report: "✓ Created knowledge/core_knowledge/{folder}/.gitkeep"
```

If `events_placement = "nested"`, create `data_models/events/.gitkeep` instead.

### 6. Create monorepo project sub-trees (if hybrid or per-project layout)

If `monorepo_layout = "hybrid"`:

```
For each project in `monorepo_projects`:
  Create `{project-root}/knowledge/core_knowledge/projects/{project_name}/`
  Inside it, create the project-scoped subset (each folder gets a `.gitkeep`):
    - components/.gitkeep
    - infrastructure/.gitkeep
    - processes/.gitkeep
```

If `monorepo_layout = "per-project"`: replicate the full baseline tree under each project (including `team_and_stakeholders/` only if the user wants per-project ownership; otherwise keep `team_and_stakeholders/` shared at the top level — Hard Rule #1 favours one place for names).

If `monorepo_layout = "single"`: skip this step.

### 7. Create private_knowledge tree

```
1. Create `{project-root}/knowledge/private_knowledge/`
2. Create sub-folders and write `.gitkeep` in each:
   - raw_data/emails_team_chats/.gitkeep
   - raw_data/screenshots/.gitkeep
   - raw_data/meetings_transcripts/.gitkeep
   - personal_tasks/.gitkeep
3. Report: "✓ Created knowledge/private_knowledge/ tree (4 .gitkeep files)"
```

### 8. Configure .gitignore at project root

The block to add:

```
# === Kipi: Private Knowledge (never commit content) ===
# Managed by Kipi. Content of private_knowledge/ stays local.
# Only .gitkeep files preserve the folder structure.
knowledge/private_knowledge/**
!knowledge/private_knowledge/
!knowledge/private_knowledge/**/
!knowledge/private_knowledge/**/.gitkeep

# === Kipi: Sidecar workflow state (not for VCS) ===
_bmad/_memory/kipi-sidecar/workflow-state.json
```

Logic:

```
IF `{project-root}/.gitignore` does NOT exist:
  Create it with the block above.
  Report: "✓ Created .gitignore at project root"

ELSE:
  Read existing .gitignore
  IF block already present (detected via marker `# === Kipi: Private Knowledge`):
    Report: "✓ .gitignore already configured (no changes)"
  ELSE:
    Append (with a leading blank line) the block above
    Report: "✓ Appended Kipi rules to existing .gitignore"
```

### 9. Update State (after each file)

After every file created, update workflow-state.json `outputs_generated` array.

---

## State Update (Final)

After all files created:

```json
{
  "current_step": "completed",
  "completed_steps": [
    {"step": "step_1_scan", "status": "completed", "timestamp": "...", "summary": "Scanned repo"},
    {"step": "step_2_propose", "status": "completed", "timestamp": "...", "summary": "Approved {{n}} folders, {{layout}} layout"},
    {"step": "step_3_implement", "status": "completed", "timestamp": "...", "summary": "Structure created"}
  ],
  "timestamps": {"completed": "{{now}}"},
  "outputs_generated": ["...", "..."],
  "gitignore_action": "created | appended | already-configured",
  "resume_instructions": "Workflow complete. Run [CS] Code Scan to populate content."
}
```

---

## Completion Report

Show user:

```
✅ **Core-Knowledge Structure Initialized!**

**Created in `knowledge/core_knowledge/`:**
- index.md (routing table + folder contracts — single source of truth)
- architecture.md
- components/, infrastructure/, processes/, team_and_stakeholders/ (with 3 placeholder files)
{{#each approved_optional_folders}}
- {{name}}/
{{/each}}

**Created in `knowledge/private_knowledge/`:**
- raw_data/{emails_team_chats,screenshots,meetings_transcripts}/ (with .gitkeep)
- personal_tasks/ (with .gitkeep)

**At project root:**
- `.gitignore` — {{gitignore_action}}

**Hard rules now in effect across all Kipi writing skills:**
- Names of people live ONLY in `team_and_stakeholders/`
- `private_knowledge/` is never referenced from `core_knowledge/`
- `private_knowledge/` content is never committed

**Authoring scaffolds available** (copy in when you start writing files in a folder):
- `_bmad/kipi/templates/folder-templates/component-template.md`
- `_bmad/kipi/templates/folder-templates/infrastructure-topic-template.md`
- `_bmad/kipi/templates/folder-templates/process-template.md`
- `_bmad/kipi/templates/folder-templates/job-template.md`

  Convention: copy a scaffold into the relevant folder as `_template.md` to give future contributors (human or agent) a starting shape.

**Next Steps:**
1. Review the structure: open `knowledge/core_knowledge/index.md` (read the Folder Contracts section)
2. Populate content: run `[CS] Code Scan Document`
3. Process raw inputs: run `[UD] Process User Data`
4. Fill knowledge gaps: run `[DI] Developer Interview`

**Quick Commands:**
- `[CS]` — Start code scan documentation
- `[ST]` — Check Core-Knowledge status
- `[MH]` — Return to menu

Would you like to start the **[CS] Code Scan** workflow now? [Y/N]
```

---

## Cleanup

After showing completion report:

1. 🧹 PURGE detailed proposal data from context
2. Keep only summary: "Initialized {{n}} folders, {{layout}} layout"
3. Return to Kipi menu (or start [CS] if user requests)

---

## Validation

Before completing, verify:

- [ ] `index.md` and `architecture.md` exist at `core_knowledge/` root
- [ ] `index.md` contains the **Folder Contracts** section, populated for every approved folder
- [ ] All baseline folders exist (components, infrastructure, processes, team_and_stakeholders) with `.gitkeep` (or content for `team_and_stakeholders/`)
- [ ] `team_and_stakeholders/` has all 3 placeholder files
- [ ] All approved optional folders exist with `.gitkeep`
- [ ] No `_about.md` files were created (contracts are inline in `index.md`)
- [ ] `private_knowledge/` tree exists with all 4 `.gitkeep` files
- [ ] `.gitignore` at project root contains the Kipi block
- [ ] No `{{placeholder}}` text remains in any generated file (except intentional TODO markers)
- [ ] workflow-state.json shows `current_step = "completed"`
- [ ] No `personal_knowledge` folder name appears anywhere

Load checklist: `{agent-root}/checklists/repository-initialize-checklist.md`

---

## Success Metrics

✅ All baseline files and folders created with proper templates
✅ All approved optional folders created with `.gitkeep`
✅ `index.md` Folder Contracts section is the only place contracts live
✅ `private_knowledge/` tree created with `.gitkeep` files
✅ `.gitignore` correctly configured (created or appended)
✅ No `_about.md` files anywhere
✅ workflow-state.json shows completed
✅ Completion report shown
✅ Hard rules announced to user

---

## Failure Modes

❌ Creating folders not in approved proposal
❌ Creating `_about.md` files (legacy pattern — contracts now live in `index.md`)
❌ Leaving `{{variable}}` placeholders in output
❌ Overwriting an existing `.gitignore` instead of appending
❌ Committing or surfacing private_knowledge content
❌ Creating an old-style `personal_knowledge/` folder

---

## Workflow Complete

This is the final step of repository-initialize.

After completion:
- User can run [CS] Code Scan to populate content
- User can run [DI] Developer Interview to fill gaps via 10 targeted questions
- User can run [ST] Status to check structure
- Workflow state preserved for reference
