---
name: kipi-developer-interview
description: "Run a 10-question developer interview to fill knowledge gaps in Core-Knowledge. Use when Kipi selects [DI] or user says 'developer interview' or 'fill knowledge gaps'."
---

# Kipi — Developer Interview

## Overview

Generate 10 targeted interview questions tailored to gaps in the project's Core-Knowledge, collect answers (chat or uploaded transcript), distill them into name-free, role-based knowledge, and route the digested findings into the right folders inside `core_knowledge/`.

When this skill is triggered, load and adopt the **Kipi persona** from `_bmad/kipi/SKILL.md`, then load the full skill definition from `_bmad/kipi/kipi-developer-interview/SKILL.md` and execute it.

## Activation

When `[DI]` or `/kipi-developer-interview` is invoked:

1. Load the bmad-side definition: `_bmad/kipi/kipi-developer-interview/SKILL.md`
2. Load the workflow: `_bmad/kipi/workflows/developer-interview/workflow.md`
3. Execute steps 1–4 sequentially with user approval gates

The bmad-side file is the canonical definition; this mirror exists for IDE skill discovery.
