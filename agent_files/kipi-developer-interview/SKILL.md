---
name: kipi-developer-interview
description: "Run a 10-question developer interview to fill knowledge gaps in Core-Knowledge. Use when Kipi selects [DI] or user says 'developer interview' or 'fill knowledge gaps'."
---

# Kipi — Developer Interview

> Modified in `LairdLuca/kipi-agent`: the gap rubric is read from the vault's own signals, and the interview writes nothing into the vault.

## Overview

Generate 10 targeted interview questions tailored to the gaps the vault declares, collect the answers (chat or a file), and hand them to the raw-notes pipeline. Answers are indications to validate, never facts.

This is the human-in-the-loop bridge between what is in the code and what only lives in a developer's head.

## Prerequisites

- The vault exists (run `[RI]` first if not) and has at least one chapter
- Recommended: have already run `[CS]` once so the gaps are well defined

## On Activation

1. Load the workflow definition: `{{KIPI_HOME}}/workflows/developer-interview/workflow.md`
2. Read `{{VAULT_DIR}}/_meta/conventions.md`, section 1.1 (the status of an answer) and section 12 (the communication log)
3. Read the state file of the branch (`{{KIPI_STATE_DIR}}/state/<slug>.json`) and offer to resume if a workflow is in progress
4. Execute the workflow steps in order and stop where it says to stop
5. Return to the Kipi menu when done

## State Management

Checkpoint into the state file of the branch after each step (see "State layout" in `{{KIPI_HOME}}/SKILL.md`). Enable resume from any step.

## User Affordance

When the questions are presented, the workflow tells the person the two ways of answering (in chat, or a file left in the raw-notes inbox) and that any question can be skipped. Do not skip that announcement.

## Hard Rules (inherited from Kipi persona)

The write list and the rules of the vault are those of `{{KIPI_HOME}}/SKILL.md` ("Where Kipi may write" and "Hard Rules"). Verbatim answers go only to `{{RAW_NOTES_DIR}}/`, never into the vault, and they carry the role label of who answered, never a name.
