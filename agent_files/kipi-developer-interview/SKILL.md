---
name: kipi-developer-interview
description: "Run a 10-question developer interview to fill knowledge gaps in Core-Knowledge. Use when Kipi selects [DI] or user says 'developer interview' or 'fill knowledge gaps'."
---

# Kipi — Developer Interview

## Overview

Generate 10 targeted interview questions tailored to gaps in the project's Core-Knowledge, collect answers (chat or transcript), distill the answers into name-free, role-based knowledge, and route the digested findings into the right folders inside `core_knowledge/`. Raw transcripts and verbatim answers stay in `private_knowledge/raw_data/meetings_transcripts/`.

This is the human-in-the-loop bridge between what's in code and what only lives in a developer's head.

## Prerequisites

- Core-Knowledge structure must exist (run `[RI]` first if not)
- `index.md` must be present
- Recommended: have already run `[CS] Code Scan Document` once so gaps are well-defined

## On Activation

1. Load the workflow definition: `_bmad/kipi/workflows/developer-interview/workflow.md`
2. Load the checklist: `_bmad/kipi/checklists/developer-interview-checklist.md`
3. Load the question template: `_bmad/kipi/templates/developer-interview/questions-template.md`
4. Load the answer-capture template: `_bmad/kipi/templates/developer-interview/answers-template.md`
5. Check for existing `workflow-state.json` — offer resume if in-progress
6. Execute the workflow steps sequentially:
   - `_bmad/kipi/workflows/developer-interview/step-01-analyze-gaps.md`
   - `_bmad/kipi/workflows/developer-interview/step-02-generate-questions.md`
   - `_bmad/kipi/workflows/developer-interview/step-03-collect-answers.md`
   - `_bmad/kipi/workflows/developer-interview/step-04-integrate.md`
7. Validate against checklist on completion
8. Return to Kipi menu when done

## State Management

Initialize and maintain `{project-root}/_bmad/_memory/kipi-sidecar/workflow-state.json`:
- Set `workflow_type: "developer-interview"`
- Track `gaps_detected`, `questions_generated`, `answer_method` (chat | transcript), `answers_collected`, `integration_proposals`
- Enable resume from any step

## User Affordance

When the questions are presented, the skill MUST tell the user upfront:

> "You can answer each question right here in chat — type as much or as little as you want. Or upload a transcript file (meeting notes, recorded interview, voice memo transcription) into `knowledge/private_knowledge/raw_data/meetings_transcripts/` and I'll digest the answers from there. **You don't have to answer any question that doesn't seem relevant — just say 'skip' (or skip it in your transcript). I'll move on.**"

## Hard Rules (inherited from Kipi persona)

These rules apply when this skill writes into `core_knowledge/`. See `_bmad/kipi/SKILL.md` for the full definition.

1. **Names of people** — verbatim answers may contain names; the digested representation written into `core_knowledge/` MUST use roles. Identity-bearing content (contacts, ownership) is routed into `team_and_stakeholders/` instead.
2. **No `private_knowledge/` references** — even though answers may be stored as transcripts in `private_knowledge/raw_data/meetings_transcripts/`, the resulting `core_knowledge/` files must NOT link to or path-mention them. They must be self-contained.
3. **Self-check before writing** — every generated `core_knowledge/` file is scanned for human names outside `team_and_stakeholders/` and any `private_knowledge` paths; redact, reroute, or refuse to write if found.
