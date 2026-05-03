---
name: kipi-repository-initialize
description: "Initialize Core-Knowledge structure for a repository. Use when Kipi selects [RI] or user says 'initialize core-knowledge' or 'repo init'."
---

# Kipi — Repository Initialize

## Overview

Initialize Core-Knowledge structure for a new repository by scanning the codebase, proposing a tailored chapter structure, and implementing it with user approval at each step.

## On Activation

1. Load the workflow definition from `_bmad/kipi/workflows/repository-initialize/workflow.md`
2. Load the checklist from `_bmad/kipi/checklists/repository-initialize-checklist.md`
3. Load the index template from `_bmad/kipi/templates/index-template.md`
4. Check for existing `workflow-state.json` — offer resume if in-progress
5. Execute the workflow steps sequentially, loading each step file as needed:
   - `_bmad/kipi/workflows/repository-initialize/step-01-scan.md`
   - `_bmad/kipi/workflows/repository-initialize/step-02-propose.md`
   - `_bmad/kipi/workflows/repository-initialize/step-03-implement.md`
6. Validate against checklist on completion
7. Return to Kipi menu when done

## State Management

Initialize and maintain `{project-root}/_bmad/_memory/kipi-sidecar/workflow-state.json`:
- Set `workflow_type: "repository-initialize"`
- Update `current_step` after each step
- Track `findings` and `outputs_generated`
- Enable resume from any step on interruption

## Hard Rules (inherited from Kipi persona)

These rules apply to every file generated during initialization. See `_bmad/kipi/SKILL.md` for the full definition.

1. **Names of people** appear ONLY in `core_knowledge/team_and_stakeholders/` (in `contacts.md`, `team-structure.md`, or `ownership-map.md`). All other Core-Knowledge files use roles.
2. **No `private_knowledge/` references** — no file in `core_knowledge/` may link to, cite, embed, or path-mention `private_knowledge/`.
3. **`private_knowledge/` content is git-ignored** — `.gitignore` is configured during step-03; only `.gitkeep` files preserve folder structure.
4. **Self-check before writing** — every generated file (templates, `_about.md`, placeholders) is checked for the rules above before save.
