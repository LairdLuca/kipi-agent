---
name: kipi-code-scan-document
description: "Deep scan repository and create comprehensive documentation. Use when Kipi selects [CS] or user says 'code scan' or 'scan and document'."
---

# Kipi — Code Scan Document

## Overview

Perform a comprehensive scan of the repository codebase and generate deep documentation for each Core-Knowledge chapter. Uses a batched, write-as-you-go approach to handle large codebases efficiently.

## Prerequisites

- Core-Knowledge structure must exist (run [RI] first if not)
- `index.md` must list chapters to populate

## On Activation

1. Load the workflow definition from `_bmad/kipi/workflows/code-scan-document/workflow.md`
2. Load the checklist from `_bmad/kipi/checklists/code-scan-checklist.md`
3. Load the batching config from `_bmad/kipi/data/batching-config.yaml`
4. Load templates: `_bmad/kipi/templates/chapter-template.md` and `_bmad/kipi/templates/index-template.md`
5. Check for existing `workflow-state.json` — offer resume if in-progress
6. Execute the workflow steps sequentially:
   - `_bmad/kipi/workflows/code-scan-document/step-01-map.md`
   - `_bmad/kipi/workflows/code-scan-document/step-02-plan-chapters.md`
   - `_bmad/kipi/workflows/code-scan-document/step-03-write.md`
7. Validate against checklist on completion
8. Return to Kipi menu when done

## State Management

Initialize and maintain `{project-root}/_bmad/_memory/kipi-sidecar/workflow-state.json`:
- Set `workflow_type: "code-scan-document"`
- Track `scan_level`, `approval_mode`, `chapters_progress`, `batches_completed`
- Enable resume from any step, chapter, or batch on interruption

## Context Management

After each batch: write findings to disk, update state, purge detailed content from memory, keep only 1-2 sentence summaries.

## Hard Rules (inherited from Kipi persona)

These rules apply to every file written by this skill. See `_bmad/kipi/SKILL.md` for the full definition.

1. **Names of people** appear ONLY in `core_knowledge/team_and_stakeholders/`. All other Core-Knowledge files reference roles (e.g. "the platform team lead").
2. **No `private_knowledge/` references** — no file in `core_knowledge/` may link to, cite, embed, or path-mention anything inside `knowledge/private_knowledge/`.
3. **Self-check before writing** — scan generated content for human names outside `team_and_stakeholders/` and any `private_knowledge` paths; redact or refuse to write if found.
