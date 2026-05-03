---
name: kipi-process-user-data
description: "Process new files in user-data/ folder into structured documents. Use when Kipi selects [UD] or user says 'process user data' or 'process new files'."
---

# Kipi — Process User Data

## Overview

Process files dropped into the `user-data/` folder, transforming them into structured, classified documents ready for integration into Core-Knowledge.

## On Activation

1. Load the workflow definition from `_bmad/kipi/workflows/process-user-data/workflow.md`
2. Load the checklist from `_bmad/kipi/checklists/process-user-data-checklist.md`
3. Load the classification rules from `_bmad/kipi/data/knowledge-classification.csv`
4. Load the template from `_bmad/kipi/templates/processed-file-template.md`
5. Load `_bmad/_memory/kipi-sidecar/processed-files.md` for tracking
6. Check for existing `workflow-state.json` — offer resume if in-progress
7. Execute the workflow steps sequentially:
   - `_bmad/kipi/workflows/process-user-data/step-01-process.md`
8. Validate against checklist on completion
9. Return to Kipi menu when done

## State Management

Initialize and maintain `{project-root}/_bmad/_memory/kipi-sidecar/workflow-state.json`:
- Set `workflow_type: "process-user-data"`
- Track `files_to_process`, `files_processed`, `files_failed`
- Enable resume if interrupted mid-processing

## File Operations

For each processed file:
1. Save structured output to `ready-for-classify/{content-type}/`
2. Move original to `_archive/`
3. Update `processed-files.md` with timestamp and content type
4. Purge file contents from context, keep only summary

## Hard Rules (inherited from Kipi persona)

When transforming user-data files into Core-Knowledge candidates, see `_bmad/kipi/SKILL.md` for the full definition.

1. **Names of people** appear ONLY in `core_knowledge/team_and_stakeholders/`. When output is destined for `core_knowledge/`, replace names with roles and route identity-bearing content into the `team_and_stakeholders/` folder.
2. **No `private_knowledge/` references** — output destined for `core_knowledge/` must never link to or path-mention `private_knowledge/`. Raw inputs may live there, but their digested representation must be self-contained.
3. **Self-check before writing** — scan every generated file for human names outside `team_and_stakeholders/` and any `private_knowledge` paths; redact, reroute, or refuse to write if found.
