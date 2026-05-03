# Repository Initialize Workflow — Validation Checklist

**Workflow:** `repository-initialize`
**Purpose:** Ensure the Tier-1 / Tier-2 Core-Knowledge structure is created correctly with `private_knowledge/` tree and gitignore guardrails.

---

## Pre-Initialization Checks

- [ ] Confirmed project root directory is correct
- [ ] Checked for existing `knowledge/core_knowledge/` folder
- [ ] If exists: asked user about fresh start vs incremental update
- [ ] Sidecar files accessible (`_bmad/_memory/kipi-sidecar/`)
- [ ] Noted whether legacy `knowledge/personal_knowledge/` exists (do NOT migrate here — that's the job of `cleanup-knowledge` drift detection)

---

## Step 1: Repository Scan

### Scan Completeness
- [ ] Scanned root directory structure
- [ ] Identified primary programming language(s)
- [ ] Identified framework(s) if applicable
- [ ] Counted total source files
- [ ] Identified key directories (src/, lib/, tests/, etc.)
- [ ] Checked for existing documentation (README, docs/)

### Monorepo & Tier-2 Signal Detection
- [ ] Checked for monorepo signals (workspace tools, multiple manifests, `packages/`/`apps/`/`services/`/`libs/`)
- [ ] Recorded `monorepo_signals.detected` (true/false) with evidence
- [ ] Checked for Tier-2 trigger signals:
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
- [ ] `findings.repository_analysis` populated (including monorepo_signals + tier2_signals)

---

## Step 2: Structure Proposal

### Gate A — Tier-1 Baseline
- [ ] Tier-1 baseline announced (2 root files + 4 root folders + private_knowledge tree)
- [ ] User confirmed Tier-1 (no editing of baseline allowed)
- [ ] State updated: `tier1_confirmed = true`

### Gate B — Monorepo Layout (when applicable)
- [ ] If monorepo signals detected: presented 3 layout options (a/b/c)
- [ ] Recommendation + rationale + evidence shown
- [ ] User explicitly chose a layout (no auto-decision)
- [ ] State updated: `monorepo_layout` = chosen value, `monorepo_projects` populated

### Gate C — Tier-2 Candidates (one-at-a-time)
- [ ] Each applicable Tier-2 candidate presented INDIVIDUALLY
- [ ] For each: recommendation, rationale, scan evidence shown
- [ ] User decided each separately (Y/N/Explain)
- [ ] `events/` candidate offered the separate-vs-nested-vs-skip choice
- [ ] State updated: `tier2_decisions` map populated with all decisions

### Gate D — Final Approval
- [ ] Full structure tree previewed (including all approved folders + `private_knowledge/` + `.gitignore`)
- [ ] User explicitly approved
- [ ] State updated: `user_approved = true`, `approval_timestamp` set

---

## Step 3: Structure Implementation

### Tier-1 Root Files
- [ ] `knowledge/core_knowledge/index.md` created from `index-template.md`
  - [ ] Routing table populated
  - [ ] Mission paragraph filled (or marked TODO)
  - [ ] No `{{variable}}` placeholders remain (except intentional TODO markers)
- [ ] `knowledge/core_knowledge/architecture.md` created from `architecture-template.md`

### Tier-1 Folders
- [ ] `components/` exists with `_about.md` from template
- [ ] `infrastructure/` exists with `_about.md` from template
- [ ] `processes/` exists with `_about.md` from template
- [ ] `team_and_stakeholders/` exists with `_about.md` + 3 placeholder files:
  - [ ] `contacts.md`
  - [ ] `team-structure.md`
  - [ ] `ownership-map.md`

### Tier-2 Folders
- [ ] Every approved Tier-2 folder exists with its generated `_about.md`
- [ ] If `events` decision = `nested`: lives at `data_models/events/_about.md`
- [ ] No Tier-2 folder created without explicit user approval

### Monorepo Layout (if applicable)
- [ ] If layout = "c" (hybrid): `core_knowledge/projects/<name>/` per project, with components/, infrastructure/, processes/ sub-trees
- [ ] If layout = "b" (per-project): full Tier-1 structure under each project
- [ ] If layout = "a"/"single": no extra project tree

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
- [ ] No folder created outside the approved proposal
- [ ] No content committed inside `private_knowledge/` (only `.gitkeep`)
- [ ] No reference to `private_knowledge/` from any file in `core_knowledge/`
- [ ] No people's names in any `core_knowledge/` file outside `team_and_stakeholders/`

### File Verification
- [ ] All routing-table entries in `index.md` point to real folders/files
- [ ] All `_about.md` files render as valid Markdown
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

### Quality Issues (Should Fix)
- [ ] ⚠️ Routing table is genuinely useful (each row tells an agent what's there vs not)
- [ ] ⚠️ Each `_about.md` clearly delimits scope (what does NOT go here)
- [ ] ⚠️ Tier-2 folder names match the approved canonical list

---

## Completion Criteria

All of the following must be true:
1. ✅ Repository scanned and signals (monorepo + Tier-2) detected
2. ✅ Tier-1 baseline confirmed by user
3. ✅ Monorepo layout explicitly chosen (if applicable)
4. ✅ Each Tier-2 candidate decided individually
5. ✅ User explicitly approved at Gate D
6. ✅ All Tier-1 files and folders created with proper templates
7. ✅ All approved Tier-2 folders exist with `_about.md`
8. ✅ Private knowledge tree created with `.gitkeep` files
9. ✅ `.gitignore` configured (created or appended idempotently)
10. ✅ Hard rules announced in completion report
11. ✅ workflow-state.json shows `completed`

---

## Sign-Off

- [ ] All checklist items verified
- [ ] User confirmed initialization complete
- [ ] Ready for code-scan-document, developer-interview, or process-user-data workflows
