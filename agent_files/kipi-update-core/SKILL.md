---
name: kipi-update-core
description: "Analyze code changes or new user data and propose Core-Knowledge updates. Use when Kipi selects [UK] or user says 'update core', or when invoked via /kipi-update-core."
---

# Kipi — Update Core

## Overview

Analyze recent changes (code diffs, new user-provided information, or stale-content signals) and propose specific updates to `core_knowledge/`. Every change is gated by user approval, then written with the Hard Rules self-check.

This skill is the workhorse of the Kipi Handshake Protocol — other agents in the project suggest invoking it after they make code changes.

## On Activation

### Step 1: Load context

1. Load `{project-root}/_bmad/_memory/kipi-sidecar/processed-files.md` (history of past updates).
2. Load `{project-root}/knowledge/core_knowledge/index.md` (current structure + Folder Contracts).
3. If either is missing → tell the user Kipi needs initialization first; suggest activating the Kipi agent and selecting `[RI] Repository Initialize`.

### Step 2: Determine what triggered this update

The user's invocation usually fits one of three patterns:

- **Code changes** — Review recent git diffs / file modifications. Identify which areas of the codebase were affected. Map changes to entries in `index.md`'s Routing Table and Folder Contracts.
- **New information** — The user has provided project-level info in the conversation (a decision, an ownership change, a process tweak). Categorise the information; determine which file(s) it belongs in.
- **Periodic sync** — Compare Core-Knowledge file timestamps with corresponding source-file modification times. Flag stale sections.

If unclear, ask the user briefly which one applies.

### Step 3: Consult the routing table and contracts

Read `index.md` and determine:
- Which existing files are affected by the changes.
- Whether new files need to be created (and which folder they belong in, per the Folder Contracts section).
- Whether the structure itself needs to change (a new optional folder, a new sub-folder under `components/`, etc.).

If the change adds a new optional folder that's not yet enabled in `index.md`'s Folder Contracts: this is a structure-level change. Don't auto-add — surface as a separate proposal for the user to approve.

### Step 4: Draft proposal

Present a clear, structured proposal:

```
## Proposed Core-Knowledge Updates

### Files to Update
- `knowledge/core_knowledge/<path>.md` — <what will change> (rationale)

### New Files to Create
- `knowledge/core_knowledge/<path>.md` — <what it will contain> (rationale)

### Structure Changes (if any)
- <e.g. add new optional folder, add new sub-folder under components/>

### index.md changes
- Routing table updates / coverage table updates / Folder Contracts updates

### Rationale
<why these updates are needed; tie back to the trigger>
```

### Step 5: User consent

**CRITICAL:** Wait for user approval before making any changes.

Ask: `Do you approve these updates? [Y]es / [N]o / [M]odify`

- **[Y]** — Proceed to Step 6.
- **[N]** — Ask what concerns they have; revise; re-present.
- **[M]** — Ask what they want to change; revise; re-present.

### Step 6: Apply updates

For each approved change:

1. **Before writing:** State exactly what will be written and to which path.
2. **Self-check Hard Rules:** scan content for human names outside `team_and_stakeholders/` and any `private_knowledge` paths. If found, redact, reroute identity to `team_and_stakeholders/`, or refuse to write.
3. **Write the file.**
4. **Confirm:** report `✓ <path> — <one-line summary>`.

If a structural change was approved (new folder), also add the corresponding row to:
- `index.md` Routing Table
- `index.md` Coverage table
- `index.md` Folder Contracts section (use the catalog defaults from `_bmad/kipi/workflows/repository-initialize/step-02-propose.md`)

### Step 7: Update tracking

1. If `index.md` changed: write the new version (with self-check).
2. Append an entry to `_bmad/_memory/kipi-sidecar/processed-files.md` with timestamp, trigger type, and list of files updated.
3. Show summary report.

---

## Hard Rules (inherited from Kipi persona)

These rules apply to every update written by this skill. See `_bmad/kipi/SKILL.md` for the full definition.

1. **Names of people** appear ONLY in `core_knowledge/team_and_stakeholders/`. All other Core-Knowledge files reference roles.
2. **No `private_knowledge/` references** — no file in `core_knowledge/` may link to, cite, embed, or path-mention anything inside `knowledge/private_knowledge/`.
3. **Self-check before writing** — scan generated content for human names outside `team_and_stakeholders/` and any `private_knowledge` paths; redact or refuse to write if found.

---

## Output Format

After completing the update, display:

```
✅ **Core-Knowledge Updated**

### Changes Applied
- <list of files updated/created>

### Summary
<brief description of what was updated and why>

### Next Steps
- Run [ST] to see the full coverage report
- Run [CL] periodically for maintenance
```

---

## Error Handling

- **Core-Knowledge doesn't exist** — "Knowledge structure not found. Activate Kipi and run `[RI] Repository Initialize` to set up the knowledge base."
- **No changes detected** — "Core-Knowledge appears up to date. No updates proposed."
- **User declines all updates** — "Understood. No changes made. Run `/kipi-update-core` again whenever you want to sync."
- **Self-check fails** — Surface the violation, ask the user how to handle (rewrite as role / route identity / drop the change).
