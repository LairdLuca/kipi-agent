---
name: kipi-code-scan-document
description: "Deep scan repository and create comprehensive documentation. Use when Kipi selects [CS] or user says 'code scan' or 'scan and document'."
---

# Kipi — Code Scan Document

> Modified on the branch `onegate` of `LairdLuca/kipi-agent`: the steps live in one workflow file, capture is by git branch, state is the state file of the branch.

## Overview

Scan one area of the code repository `{{CODE_REPO}}` and write one chapter (or one variant file of a chapter) of the vault, with deep content backed by source reads. One chapter per invocation.

## Prerequisites

- The vault exists: `{{VAULT_DIR}}/index.md` and `{{VAULT_DIR}}/_meta/conventions.md` (run `[RI]` first if not)

## On Activation

1. Load the workflow definition from `{{KIPI_HOME}}/workflows/code-scan-document/workflow.md`
2. Load the capture mechanics from `{{KIPI_HOME}}/workflows/capture.md`
3. Read `{{VAULT_DIR}}/_meta/conventions.md` before the first write of the session
4. Read the state file of the branch (`{{KIPI_STATE_DIR}}/state/<slug>.json`, `default` in a single-branch project) and offer to resume if a workflow is in progress
5. Execute the workflow
6. Return to the Kipi menu when done

## State Management

Checkpoint into the state file of the branch after each major step (see "State layout" in `{{KIPI_HOME}}/SKILL.md`). Enable resume from any step on interruption.

## Context Management

After each batch of reads: write findings to disk, update state, purge detailed content from memory, keep only 1-2 sentence summaries.

## Hard Rules (inherited from Kipi persona)

The write list and the rules of the vault are those of `{{KIPI_HOME}}/SKILL.md` ("Where Kipi may write" and "Hard Rules"). Nothing in this skill widens them.
