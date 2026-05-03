# Repository Initialize Workflow

**Workflow ID:** `repository-initialize`
**Purpose:** Initialize Core-Knowledge structure for a new repository
**Menu Trigger:** `[RI]` or fuzzy match on `repo-init`

---

## Overview

This workflow scans a repository, proposes a tailored Core-Knowledge structure, and implements it with user approval at each step.

---

## Configuration

```yaml
workflow_type: repository-initialize
state_file: {project-root}/_bmad/_memory/kipi-sidecar/workflow-state.json
output_folder: {project-root}/knowledge/core_knowledge/
templates:
  index: {agent-root}/templates/index-template.md
checklist: {agent-root}/checklists/repository-initialize-checklist.md
```

---

## Workflow Architecture

This uses **micro-file architecture** for disciplined execution:

- Each step is a self-contained file with embedded rules
- Sequential progression with user control at each step
- State tracked in workflow-state.json
- You NEVER proceed to next step without user approval

---

## Prerequisites

Before starting:
1. Confirm project root directory with user
2. Check if knowledge/core_knowledge/ already exists
3. If exists: ask user about fresh start vs update

---

## Execution Steps

### Step 1: Scan Repository
**File:** `./step-01-scan.md`

- Scan repository structure and characteristics
- Identify language, framework, patterns
- Present scan summary to user
- ⏸️ STOP for acknowledgment

### Step 2: Propose Structure
**File:** `./step-02-propose.md`

- Generate tailored chapter structure
- Present as tree diagram with rationale
- ⏸️ STOP for user approval or changes

### Step 3: Implement Structure
**File:** `./step-03-implement.md`

- Create knowledge/core_knowledge/ folder
- Generate index.md from template
- Create placeholder files for each chapter
- ⏸️ STOP with completion summary

---

## Initialization

When this workflow is triggered:

1. Check for existing workflow-state.json
2. If exists with workflow_type="repository-initialize" and not completed:
   - Ask: "Resume from {{current_step}} or start fresh?"
3. If starting fresh:
   - Initialize new workflow-state.json
   - Load and execute `./step-01-scan.md`

---

## State Management

After each step:
```json
{
  "workflow_type": "repository-initialize",
  "current_step": "step_X_name",
  "completed_steps": [...],
  "findings": { ... },
  "resume_instructions": "..."
}
```

---

## Completion

When Step 3 completes:
1. Validate against checklist
2. Set workflow state to completed
3. Inform user: "Core-Knowledge structure initialized. Ready for [CS] Code Scan."
4. Return to Kipi menu

---

## Error Handling

- If any step fails: save state, inform user, offer retry
- If user cancels: save partial progress, return to menu
- State file enables resume from any step

