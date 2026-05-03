# Step 3: Implement Structure

## MANDATORY EXECUTION RULES (READ FIRST)

- 🛑 NEVER create folders/files outside the approved structure from Step 2
- ✅ ALWAYS use the templates from `{agent-root}/templates/` for all generated files
- 📋 SHOW user the full creation preview before creating anything
- 🔄 UPDATE workflow-state.json after each file created
- 🧹 PURGE detailed proposal data from context after completion; keep only the summary
- 🔒 Treat `private_knowledge/` content as never-to-commit. Only `.gitkeep` files preserve folder structure.
- ➕ When `.gitignore` already exists at project root: APPEND idempotently with a header comment, log the action in the completion report. Never overwrite.

---

## Prerequisites

- Step 2 (Propose) must be completed
- workflow-state.json must show:
  - `structure_proposal.tier1_confirmed = true`
  - `structure_proposal.user_approved = true`
  - `structure_proposal.approval_timestamp` is set

---

## Your Task

Create the approved Core-Knowledge structure: 2 root files, 4 Tier-1 folders (each with `_about.md`), the 3 mandatory `team_and_stakeholders/` initial files, every approved Tier-2 folder (with `_about.md`), the full `private_knowledge/` tree (with `.gitkeep` files), and the `.gitignore` rules at project root.

---

## Templates Inventory

| Template | Used for |
|---|---|
| `{agent-root}/templates/index-template.md` | Root `index.md` |
| `{agent-root}/templates/architecture-template.md` | Root `architecture.md` |
| `{agent-root}/templates/about-templates/components-about.md` | `components/_about.md` |
| `{agent-root}/templates/about-templates/infrastructure-about.md` | `infrastructure/_about.md` |
| `{agent-root}/templates/about-templates/processes-about.md` | `processes/_about.md` |
| `{agent-root}/templates/about-templates/team-and-stakeholders-about.md` | `team_and_stakeholders/_about.md` |
| `{agent-root}/templates/team-and-stakeholders/contacts-template.md` | `team_and_stakeholders/contacts.md` |
| `{agent-root}/templates/team-and-stakeholders/team-structure-template.md` | `team_and_stakeholders/team-structure.md` |
| `{agent-root}/templates/team-and-stakeholders/ownership-map-template.md` | `team_and_stakeholders/ownership-map.md` |

For Tier-2 folders, generate `_about.md` inline using the contract pattern below.

---

## Execution Sequence

### 1. Verify Approval

Check workflow-state.json:
- `structure_proposal.user_approved` must be `true`
- If not: return to Step 2

### 2. Build Creation Manifest

Construct the full list of files & folders to create. The manifest depends on the approved structure (Tier-1 baseline, monorepo layout, approved Tier-2 folders).

### 3. Show Creation Preview

```
📝 **Creation Preview**

Will create the following in `{{project_root}}`:

knowledge/
├── core_knowledge/
│   ├── index.md                                         (from index-template.md)
│   ├── architecture.md                                  (from architecture-template.md)
│   ├── components/
│   │   └── _about.md                                    (folder contract)
│   ├── infrastructure/
│   │   └── _about.md                                    (folder contract)
│   ├── processes/
│   │   └── _about.md                                    (folder contract)
│   ├── team_and_stakeholders/
│   │   ├── _about.md                                    (folder contract)
│   │   ├── contacts.md                                  (placeholder)
│   │   ├── team-structure.md                            (placeholder)
│   │   └── ownership-map.md                             (placeholder)
{{#each approved_tier2_folders}}
│   ├── {{name}}/
│   │   └── _about.md                                    (folder contract — generated)
{{/each}}
{{#if monorepo_hybrid}}
│   └── projects/
{{#each monorepo_projects}}
│       └── {{project_name}}/
│           ├── components/_about.md
│           ├── infrastructure/_about.md
│           └── processes/_about.md
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

### 4. Create core_knowledge Tier-1

```
1. Create `{project-root}/knowledge/core_knowledge/`
2. Write `index.md` from index-template.md, substituting:
   - {{project_name}}
   - {{repository_type}}
   - {{primary_language}}
   - {{date}}
   - {{project_mission_1_paragraph}}  ← if not yet known, leave a TODO marker
   - tier2_folders_present block iterates over approved Tier-2 folders
3. Write `architecture.md` from architecture-template.md
4. For each Tier-1 folder (`components/`, `infrastructure/`, `processes/`, `team_and_stakeholders/`):
   a. Create folder
   b. Write `_about.md` from the matching template
5. Inside `team_and_stakeholders/`:
   - Write `contacts.md` from contacts-template.md
   - Write `team-structure.md` from team-structure-template.md
   - Write `ownership-map.md` from ownership-map-template.md
6. Report each file created:
   "✓ Created knowledge/core_knowledge/index.md"
   "✓ Created knowledge/core_knowledge/architecture.md"
   "✓ Created knowledge/core_knowledge/components/_about.md"
   ...
```

### 5. Create core_knowledge Tier-2 (per approval)

For each approved Tier-2 folder:

```
a. Create `{project-root}/knowledge/core_knowledge/{folder}/`
b. Generate `_about.md` inline using the Tier-2 contract pattern (see appendix at end of this file)
c. Write the file
d. Report: "✓ Created knowledge/core_knowledge/{folder}/_about.md"
```

If `events` decision = `nested`, create `data_models/events/` instead and put the `_about.md` there.

### 6. Create monorepo project sub-trees (if hybrid layout)

If `monorepo_layout = "c"` (hybrid):

```
For each project in `monorepo_projects`:
  Create `{project-root}/knowledge/core_knowledge/projects/{project_name}/`
  Inside it, create the project-scoped subset:
    - components/_about.md (use components-about template; project-scoped)
    - infrastructure/_about.md
    - processes/_about.md
```

If `monorepo_layout = "b"` (per-project): full Tier-1 structure under each project.
If `monorepo_layout = "a"` or `"single"`: skip this step.

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

`.gitkeep` files contain nothing (or a single comment line if needed). Their job is to keep empty folders in git.

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
    {"step": "step_2_propose", "status": "completed", "timestamp": "...", "summary": "Tier-1 baseline + {{n}} Tier-2 folders + {{layout}} layout"},
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
- index.md (routing table)
- architecture.md
- components/, infrastructure/, processes/, team_and_stakeholders/
{{#each approved_tier2_folders}}
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

**Next Steps:**
1. Review the structure: open `knowledge/core_knowledge/index.md`
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
2. Keep only summary: "Initialized {{n}} chapters, {{m}} Tier-2 folders, {{layout}} layout"
3. Return to Kipi menu (or start [CS] if user requests)

---

## Validation

Before completing, verify:

- [ ] `index.md` and `architecture.md` exist at `core_knowledge/` root
- [ ] All Tier-1 folders exist with `_about.md`
- [ ] `team_and_stakeholders/` has all 3 placeholder files
- [ ] All approved Tier-2 folders have `_about.md`
- [ ] `private_knowledge/` tree exists with all 4 `.gitkeep` files
- [ ] `.gitignore` at project root contains the Kipi block
- [ ] No `{{placeholder}}` text remains in any generated file
- [ ] workflow-state.json shows `current_step = "completed"`
- [ ] No `personal_knowledge` folder name appears anywhere

Load checklist: `{agent-root}/checklists/repository-initialize-checklist.md`

---

## Success Metrics

✅ All Tier-1 files and folders created with proper templates
✅ All approved Tier-2 folders created with `_about.md`
✅ `private_knowledge/` tree created with `.gitkeep` files
✅ `.gitignore` correctly configured (created or appended)
✅ No unapproved folders created
✅ workflow-state.json shows completed
✅ Completion report shown
✅ Hard rules announced to user

---

## Failure Modes

❌ Creating folders not in approved proposal
❌ Leaving `{{variable}}` placeholders in output
❌ Overwriting an existing `.gitignore` instead of appending
❌ Committing or surfacing private_knowledge content
❌ Creating an old-style `personal_knowledge/` folder
❌ Forgetting `_about.md` in any Tier-1 or approved Tier-2 folder

---

## Appendix — Tier-2 `_about.md` generator pattern

When creating a Tier-2 `_about.md` inline, use this pattern:

```markdown
# {{folder_title}} — Folder Contract

{{1-paragraph scope statement}}

## What goes here

- {{bullet}}
- {{bullet}}

## What does NOT go here

- {{bullet pointing to the right folder}}

## Hard rules

- Reference roles, never names. Names live ONLY in `team_and_stakeholders/`.
- Never link to anything inside `private_knowledge/`.
```

Pre-baked content per Tier-2 folder:

| Folder | Scope statement |
|---|---|
| `operations/` | Runbooks, monitoring, alerting, on-call procedures, incident playbooks. The "what to do when something breaks" knowledge. |
| `integrations/` | External systems this repo talks to (3rd-party APIs, internal services, message brokers, identity providers). Each integration is one file with auth model, rate limits, failure modes. |
| `api/` | Public APIs this repo exposes (REST/GraphQL/gRPC). Endpoint catalog, request/response shapes, versioning, auth, error contracts. |
| `data_models/` | Schemas, ORM models, and notable queries. Entity relationships, indexes, migrations strategy, query patterns used in code. |
| `events/` | Topics / queues this repo produces or consumes. Topic catalog, schemas, producers, consumers, retention, DLQ behavior. |
| `security/` | Authentication, authorization, secret handling, threat model, compliance posture. |
| `ml/` | Model training, inference, evaluation, datasets, feature engineering, model lifecycle. |
| `cli/` | Command-line interface: subcommands, flags, exit codes, configuration, examples. |

---

## Workflow Complete

This is the final step of repository-initialize.

After completion:
- User can run [CS] Code Scan to populate content
- User can run [DI] Developer Interview to fill gaps via 10 targeted questions
- User can run [ST] Status to check structure
- Workflow state preserved for reference
