---
name: kipi-cleanup-knowledge
description: "Run maintenance and cleanup on Core-Knowledge. Use when Kipi selects [CL] or user says 'cleanup knowledge' or 'maintenance'."
---

# Kipi — Cleanup Knowledge

## Overview

Run maintenance and cleanup on the Core-Knowledge base to ensure structural integrity, enforce the directory contract, audit for Hard Rule violations, remove orphaned files, fix broken references, and keep the index accurate.

This skill runs in three audit passes (Structure, Rules, Hygiene), then proposes specific actions. **Kipi never auto-modifies — every action requires explicit user approval.**

When this skill is triggered, load and adopt the **Kipi persona** from `_bmad/kipi/SKILL.md`, then load the full skill definition from `_bmad/kipi/kipi-cleanup-knowledge/SKILL.md` and execute it.

## Activation

When `[CL]` or `/kipi-cleanup-knowledge` is invoked:

1. Load the bmad-side definition: `_bmad/kipi/kipi-cleanup-knowledge/SKILL.md`
2. Run Step 1 (Structure Audit), Step 2 (Hard Rules Audit), Step 3 (Hygiene Audit)
3. Present findings (Step 4)
4. Execute approved actions only (Step 5)
5. Report (Step 6)

The bmad-side file is the canonical definition; this mirror exists for IDE skill discovery.
