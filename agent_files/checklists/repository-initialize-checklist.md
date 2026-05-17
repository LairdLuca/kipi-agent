# Repository Initialize Workflow — Validation Checklist

**Workflow:** `repository-initialize`
**Purpose:** Ensure the Core-Knowledge structure is created correctly with `private_knowledge/` tree and gitignore guardrails. Folder contracts are recorded **once** in `index.md`; per-folder `_about.md` files are no longer used.

---

## Pre-Initialization Checks

- [ ] Confirmed project root directory is correct
- [ ] Checked for existing `knowledge/core_knowledge/` folder
- [ ] If exists: asked user about fresh start vs incremental update
- [ ] Sidecar files accessible (`_bmad/_memory/kipi-sidecar/`)
- [ ] Noted whether legacy `knowledge/personal_knowledge/` exists (do NOT migrate here — that's the job of `cleanup-knowledge` drift detection)
- [ ] Noted whether legacy `_about.md` files exist anywhere in `core_knowledge/` (do NOT migrate here — that's the job of `cleanup-knowledge`'s legacy migration)

---

## Step 1: Repository Scan

### Scan Completeness
- [ ] Scanned root directory structure
- [ ] Identified primary programming language(s)
- [ ] Identified framework(s) if applicable
- [ ] Counted total source files
- [ ] Identified key directories (src/, lib/, tests/, etc.)
- [ ] Checked for existing documentation (README, docs/)

### Monorepo & Optional-Folder Signal Detection
- [ ] Checked for monorepo signals (workspace tools, multiple manifests, `packages/`/`apps/`/`services/`/`libs/`)
- [ ] Recorded `monorepo_signals.detected` (true/false) with evidence
- [ ] Checked for optional-folder trigger signals:
  - [ ] REST/GraphQL/gRPC endpoints → `api/`
  - [ ] DB/ORM/schema files → `data_models/`
  - [ ] Kafka/MQ libraries → `events/`
  - [ ] Auth/secrets handling → `security/`
  - [ ] ML training/inference → `ml/`
  - [ ] CLI entry points → `cli/`
  - [ ] External SDK/HTTP clients → `integrations/`

### Scan Output
- [ ] Presented scan summary to user
- [ ] User acknowledged scan results before proceeding

### State Management
- [ ] workflow-state.json initialized
- [ ] `workflow_type = "repository-initialize"`
- [ ] `findings.repository_analysis` populated (including monorepo_signals + scan_signals)

---

## Step 2: Structure Proposal (single review screen)

- [ ] Single review screen rendered (no multi-gate ceremony)
- [ ] Baseline rows (mandatory) shown as fixed/checked
- [ ] Optional folder rows pre-selected based on signal evidence; user can toggle
- [ ] Monorepo layout selectable on the same screen (single / per-project / hybrid)
- [ ] `events/` placement (separate vs nested under `data_models/`) shown when `events/` is selected
- [ ] Final tree preview rendered with current selections
- [ ] User explicitly approved with `[A]`
- [ ] State updated: `structure_proposal.user_approved = true`, `approval_timestamp` set
- [ ] State captures full resolved structure: `approved_folders`, `events_placement`, `monorepo_layout`, `monorepo_projects`

---

## Step 3: Structure Implementation

### Root Files
- [ ] `knowledge/core_knowledge/index.md` created from `index-template.md`
  - [ ] Routing table populated
  - [ ] Coverage table populated for all approved folders
  - [ ] **Folder Contracts** section populated for every approved folder (baseline + optional)
  - [ ] Mission paragraph filled (or marked TODO)
  - [ ] No `{{variable}}` placeholders remain (except intentional TODO markers)
- [ ] `knowledge/core_knowledge/architecture.md` created from `architecture-template.md`

### Baseline Folders
- [ ] `components/` exists (with `.gitkeep` if empty)
- [ ] `infrastructure/` exists (with `.gitkeep` if empty)
- [ ] `processes/` exists (with `.gitkeep` if empty)
- [ ] `team_and_stakeholders/` exists with 3 placeholder files:
  - [ ] `contacts.md`
  - [ ] `team-structure.md`
  - [ ] `ownership-map.md`
- [ ] No `_about.md` files were created in any folder

### Optional Folders
- [ ] Every approved optional folder exists with a `.gitkeep`
- [ ] If `events_placement = "nested"`: lives at `data_models/events/.gitkeep` (no separate `events/` folder)
- [ ] No optional folder created without explicit user approval

### Monorepo Layout (if applicable)
- [ ] If layout = "hybrid": `core_knowledge/projects/<name>/` per project, with `components/`, `infrastructure/`, `processes/` sub-folders (each with `.gitkeep`)
- [ ] If layout = "per-project": full baseline structure under each project (and the team_and_stakeholders/ placement strategy decided with the user)
- [ ] If layout = "single": no extra project tree

### Private Knowledge Tree
- [ ] `knowledge/private_knowledge/` exists
- [ ] `private_knowledge/raw_data/emails_team_chats/.gitkeep` exists
- [ ] `private_knowledge/raw_data/screenshots/.gitkeep` exists
- [ ] `private_knowledge/raw_data/meetings_transcripts/.gitkeep` exists
- [ ] `private_knowledge/personal_tasks/.gitkeep` exists

### .gitignore at Project Root
- [ ] `.gitignore` exists at project root
- [ ] Contains the Kipi private_knowledge block (markers present)
- [ ] If pre-existing `.gitignore`: appended idempotently (not overwritten)
- [ ] State recorded: `gitignore_action` = `created | appended | already-configured`

### State Management
- [ ] `current_step = "completed"`
- [ ] `outputs_generated` lists all created files
- [ ] `gitignore_action` recorded

---

## Post-Initialization Validation

### Forbidden Items (must NOT exist)
- [ ] No `knowledge/personal_knowledge/` folder created (legacy name)
- [ ] No `_about.md` files anywhere in `core_knowledge/`
- [ ] No folder created outside the approved proposal
- [ ] No content committed inside `private_knowledge/` (only `.gitkeep`)
- [ ] No reference to `private_knowledge/` from any file in `core_knowledge/`
- [ ] No people's names in any `core_knowledge/` file outside `team_and_stakeholders/`

### File Verification
- [ ] All routing-table entries in `index.md` point to real folders/files
- [ ] All Folder Contracts entries in `index.md` correspond to a real folder on disk
- [ ] No broken internal links

### Cleanup
- [ ] Detailed proposal data purged from context
- [ ] Only summary retained in memory
- [ ] workflow-state.json updated to completed

---

## Common Issues to Check

### Critical Issues (Must Fix)
- [ ] ❌ No files created without final user approval
- [ ] ❌ No `{{placeholder}}` text in outputs (except deliberate TODO markers)
- [ ] ❌ `.gitignore` not overwritten — if it existed, must have been appended
- [ ] ❌ `private_knowledge/` content never committed
- [ ] ❌ No legacy `personal_knowledge/` references created
- [ ] ❌ No `_about.md` files created (legacy pattern)

### Quality Issues (Should Fix)
- [ ] ⚠️ Routing table is genuinely useful (each row tells an agent what's there vs not)
- [ ] ⚠️ Folder Contracts section in `index.md` clearly delimits scope (each folder has "Holds" and "Does NOT hold")
- [ ] ⚠️ Optional folder names match the approved canonical list

---

## Completion Criteria

All of the following must be true:
1. ✅ Repository scanned and signals (monorepo + optional folders) detected
2. ✅ Single review screen presented and approved by user
3. ✅ All baseline files and folders created
4. ✅ All approved optional folders exist with `.gitkeep`
5. ✅ `index.md` Folder Contracts section is populated (and is the ONLY place contracts live)
6. ✅ No `_about.md` files anywhere
7. ✅ Private knowledge tree created with `.gitkeep` files
8. ✅ `.gitignore` configured (created or appended idempotently)
9. ✅ Hard rules announced in completion report
10. ✅ Authoring scaffolds in `_bmad/kipi/templates/folder-templates/` mentioned in completion report
11. ✅ workflow-state.json shows `completed`

---

## Sign-Off

- [ ] All checklist items verified
- [ ] User confirmed initialization complete
- [ ] Ready for code-scan-document, developer-interview, or process-user-data workflows
