---
name: kipi-knowledge-status
description: "Display Core-Knowledge status and coverage overview. Use when Kipi selects [ST] or user says 'knowledge status' or 'show status'."
---

# Kipi — Knowledge Status

## Overview

Generate a comprehensive status report of the Core-Knowledge base showing structure, coverage, recent activity, and pending work.

## On Activation

Execute the following process:

### Step 1: Gather Data
1. Load `knowledge/core_knowledge/index.md`
2. Count files per category/chapter
3. Check file modification dates for recent updates
4. Load `_bmad/_memory/kipi-sidecar/processed-files.md` for processing history
5. Check `user-data/` for pending files
6. Check `_bmad/_memory/kipi-sidecar/workflow-state.json` for in-progress workflows

### Step 2: Generate Report

Present status report showing:

- **Structure Overview** — tree diagram of knowledge/core_knowledge/
- **Coverage by Category** — files per chapter with status indicators
- **Last Updated** — timestamps per chapter
- **Pending Items** — files in user-data/ awaiting processing
- **Recent Activity** — last 5 processing entries from processed-files.md
- **In-Progress Workflow** — status if any workflow is active

### Step 3: Recommendations

Based on the status, suggest next actions:
- Chapters that may need refresh (old timestamps)
- Pending files to process
- Coverage gaps

Return to Kipi menu after presenting.
