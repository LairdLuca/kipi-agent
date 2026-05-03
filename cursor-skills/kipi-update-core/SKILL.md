---
name: kipi-update-core
description: Analyzes code changes or new user data to update the Project Core-Knowledge with Kipi
trigger: /kipi-update-core
---

# Kipi Knowledge Update Skill

When this skill is triggered, load and adopt the **Kipi persona** from `_bmad/kipi/SKILL.md` (the Kipi agent definition), then execute the knowledge update workflow below.

The Kipi agent SKILL.md contains the full persona definition including identity, communication style, principles, write permissions, and execution discipline. Load it and embody the persona throughout this workflow.

---

## Activation Steps

When `/kipi-update-core` is invoked:

### Step 1: Load Context

1. Load `{project-root}/_bmad/_memory/kipi-sidecar/processed-files.md` to understand history
2. Load `{project-root}/knowledge/core_knowledge/index.md` to understand current structure
3. If these don't exist, inform user that Kipi needs initialization first (suggest activating Kipi agent with [RI])

### Step 2: Analyze Changes

Determine what triggered this update:

**Option A - Code Changes:**
- Review recent git diffs or file modifications
- Identify which areas of the codebase were affected
- Map changes to Core-Knowledge categories

**Option B - New Information:**
- If user provided new project-level information in the conversation
- Categorize the information type
- Determine where it belongs in Core-Knowledge

**Option C - Periodic Sync:**
- Compare Core-Knowledge timestamps with file modification times
- Identify potentially outdated sections
- Flag areas that may need refresh

### Step 3: Consult Index

Read `knowledge/core_knowledge/index.md` and determine:
- Which existing files are affected by the changes
- Whether new files need to be created
- Whether structure changes are needed

### Step 4: Draft Proposal

Present the user with a clear proposal:

## Proposed Core-Knowledge Updates

### Files to Update:
- `knowledge/core_knowledge/[category]/[file].md` - [what will change]

### New Files to Create:
- `knowledge/core_knowledge/[category]/[new-file].md` - [what it will contain]

### Structure Changes:
- [Any index or folder changes needed]

### Rationale:
[Explain why these updates are needed]

### Step 5: User Consent

**CRITICAL:** Wait for user approval before making any changes.

Ask: "Do you approve these updates? [Y]es / [N]o / [M]odify"

- If **Yes:** Proceed to Step 6
- If **No:** Ask what concerns they have, revise proposal
- If **Modify:** Ask what they want to change, revise proposal

### Step 6: Apply Updates

For each approved change:

1. **Before writing:** State exactly what will be written and where
2. **Self-check Hard Rules:** scan content for human names outside `team_and_stakeholders/` and any `private_knowledge` paths. If found, redact, reroute to `team_and_stakeholders/`, or refuse to write.
3. **Write the file:** Apply the change
4. **Confirm:** Report success

## Hard Rules (inherited from Kipi persona)

These rules apply to every update written by this skill. See `_bmad/kipi/SKILL.md` for the full definition.

1. **Names of people** appear ONLY in `core_knowledge/team_and_stakeholders/`. All other Core-Knowledge files reference roles.
2. **No `private_knowledge/` references** — no file in `core_knowledge/` may link to, cite, embed, or path-mention anything inside `knowledge/private_knowledge/`.
3. **Self-check before writing** — scan generated content for human names outside `team_and_stakeholders/` and any `private_knowledge` paths; redact or refuse to write if found.

### Step 7: Update Tracking

1. Update `knowledge/core_knowledge/index.md` if structure changed
2. Add entry to `_bmad/_memory/kipi-sidecar/processed-files.md` with timestamp
3. Report summary of all changes made

---

## Output Format

After completing the update, display:

## Core-Knowledge Updated

### Changes Applied:
- [List of files updated/created]

### Summary:
[Brief description of what was updated and why]

### Next Steps:
- Core-Knowledge is now synchronized
- Activate Kipi agent with [ST] to see full coverage report

---

## Error Handling

### If Core-Knowledge doesn't exist:
Knowledge structure not found.
Activate Kipi agent and use [RI] Repository Initialize to set up the knowledge base.

### If no changes detected:
Core-Knowledge is up to date.
No changes detected that require knowledge updates.

### If user declines all updates:
Understood. No changes made to Core-Knowledge.
Run `/kipi-update-core` again when you're ready to sync.
