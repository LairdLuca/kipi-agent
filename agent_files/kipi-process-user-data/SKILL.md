---
name: kipi-process-user-data
description: "Process files dropped in the raw-notes inbox into structured extractions. Use when Kipi selects [UD] or user says 'process user data' or 'process new files'."
---

# Kipi — Process User Data

> Modified in `LairdLuca/kipi-agent`: the inbox is the raw-notes folder, with a ledger, an append-only archive and a source status by channel.

## Overview

Process the files dropped into the raw-notes inbox `{{RAW_NOTES_DIR}}/`: pasted messages, call notes, documents. Each one becomes a structured extraction in `ready-for-classify/`, ready to fold into a chapter with `[UK]`.

## On Activation

1. Load the workflow definition from `{{KIPI_HOME}}/workflows/process-user-data/workflow.md`
2. Load the classification table from `{{KIPI_HOME}}/data/knowledge-classification.csv`
3. Read `{{VAULT_DIR}}/_meta/conventions.md`, section 1.1: the channel decides the status of the information
4. Read the lines of `ledger.jsonl` for the files in play (never the whole file) and the state file of the branch; offer to resume if a workflow is in progress
5. Execute the workflow
6. Return to the Kipi menu when done

## State Management

Checkpoint into the state file of the branch (see "State layout" in `{{KIPI_HOME}}/SKILL.md`) and keep one ledger line per processed file. Enable resume if interrupted mid-processing.

## File Operations

For each processed file, in this order: write `ready-for-classify/<name>.extracted.md`, move the original to `_archive/<YYYY-MM>/` (append-only, never delete from it), write its one line in `ledger.jsonl`, or edit it if the path already has one. Purge the file contents from context and keep only a summary.

## Hard Rules (inherited from Kipi persona)

The write list and the rules of the vault are those of `{{KIPI_HOME}}/SKILL.md` ("Where Kipi may write" and "Hard Rules"). Names of people never go into an extraction; sensitive data is replaced by its placeholder.
