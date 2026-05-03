# Code Scan Document Workflow

**Workflow ID:** `code-scan-document`
**Purpose:** Deep scan repository and create comprehensive documentation
**Menu Trigger:** `[CS]` or fuzzy match on `code-scan`

---

## Overview

This workflow performs a comprehensive scan of the repository codebase and generates deep documentation for each Core-Knowledge chapter. It uses a batched, write-as-you-go approach to handle large codebases efficiently.

---

## Configuration

```yaml
workflow_type: code-scan-document
state_file: {project-root}/_bmad/_memory/kipi-sidecar/workflow-state.json
output_folder: {project-root}/knowledge/core_knowledge/
templates:
  chapter: {agent-root}/templates/chapter-template.md
  index: {agent-root}/templates/index-template.md
checklist: {agent-root}/checklists/code-scan-checklist.md
```

---

## Prerequisites

Before starting:
1. Core-Knowledge structure must exist (run [RI] first)
2. index.md must list chapters to populate
3. Confirm scan scope with user

---

## Scan Levels

Offer user choice of scan depth:

| Level | Description | Time Estimate | Use Case |
|-------|-------------|---------------|----------|
| **Quick** | Pattern-based, no source reading | 5-15 min | Overview only |
| **Chapter** | Read files per chapter scope | 30-60 min | Standard documentation |
| **Exhaustive** | Read ALL source files | 1-3 hours | Deep dive, migration |

## Approval Mode

Offer user choice of approval mode:

| Mode | Description | Use Case |
|------|-------------|----------|
| **Interactive** | Approve each chapter plan and completion | Standard workflow, review as you go |
| **Auto-approve** | Automatically approve all chapters, still break into chapters | Large repos, multiple sessions, batch processing |

**Note:** Auto-approve mode still breaks work into chapters for large repositories, allowing you to process documentation over multiple chat sessions. You can review and edit chapters after they're created.

---

## Workflow Architecture

```
workflow.md (this file)
├── step-01-map.md        # Initial project mapping
├── step-02-plan-chapters.md  # Plan each chapter's content
└── step-03-write.md      # Write chapters (batched)
```

---

## Execution Steps

### Step 1: Project Mapping
**File:** `./step-01-map.md`

- Scan directory structure
- Map file organization
- Identify entry points
- Present mapping summary
- ⏸️ STOP for user approval

### Step 2: Plan Chapters
**File:** `./step-02-plan-chapters.md`

- For each chapter in index.md:
  - Define scope and source locations
  - Plan sub-chapters
  - Identify key files to document
- ⏸️ STOP after each chapter plan for approval

### Step 3: Write Chapters
**File:** `./step-03-write.md`

- For each planned chapter:
  - Process in batches by subfolder
  - Write using chapter-template
  - Validate structure
  - Update index.md
- ⏸️ STOP after each chapter completion

---

## State Management

### Initialization
```json
{
  "workflow_type": "code-scan-document",
  "scan_level": "chapter",
  "approval_mode": "interactive",
  "auto_approve": false,
  "current_step": "step_1_map",
  "findings": {
    "chapters_progress": []
  }
}
```

**approval_mode values:**
- `"interactive"` - User approves each chapter plan and completion
- `"auto_approve"` - Automatically approve all chapters (still breaks into chapters for large repos)

### Per-Chapter Progress
```json
{
  "chapters_progress": [
    {
      "chapter_name": "Overview",
      "status": "not_started|planned|in_progress|written|validated",
      "file_path": "knowledge/core_knowledge/overview.md",
      "sub_chapters_count": 5,
      "batches_completed": 3,
      "summary": "Project intro and quick start"
    }
  ]
}
```

### Batching State
```json
{
  "batches_completed": [
    {
      "path": "src/components/",
      "files_scanned": 15,
      "summary": "UI components for dashboard",
      "timestamp": "..."
    }
  ]
}
```

---

## Resumability

This workflow is fully resumable:

1. **Step-level resume:** Pick up at any step
2. **Chapter-level resume:** Skip completed chapters
3. **Batch-level resume:** Continue within a chapter

On activation, check state and offer:
```
📋 Found in-progress code scan:
- Completed: 3/8 chapters
- Current: "API Documentation" (step 3 of 5)

[R] Resume from current position
[S] Start fresh (archives old progress)
```

---

## Context Management

**Critical for large codebases:**

After each batch:
1. 💾 WRITE findings to chapter file
2. 🔄 UPDATE workflow-state.json
3. 🧹 PURGE detailed content from memory
4. 📊 KEEP only summary (1-2 sentences)

This prevents context overflow on large repos.

---

## Batching Strategy

For deep/exhaustive scans:

```
For each chapter:
  For each subfolder in scope:
    1. Read all files in subfolder
    2. Extract relevant information
    3. IMMEDIATELY append to chapter file
    4. Update state: batches_completed
    5. PURGE batch data from context
    6. Keep summary: "{files} files in {folder}"
```

**Batch Size Guidelines:**
- Max ~20 files per batch
- Large files (>3000 LOC): process individually
- Binary files: skip with note

---

## Output Quality Standards

Every chapter MUST have:

1. **Summary** (1-3 sentences at top)
2. **Table of Contents** (matching actual sub-chapters)
3. **Sub-chapters** (organized content sections)
4. **Code References** (real file paths)
5. **Change History** (at bottom)

**No Placeholders:** All {{variable}} must be filled.

## Hard Rules (CRITICAL — applied to every chapter file)

Before writing ANY file into `core_knowledge/`, perform this self-check:

1. **Names check** — Does the content contain any human first/last names? If yes, are they ONLY inside a file under `team_and_stakeholders/`? If a name appears outside `team_and_stakeholders/`: replace with the person's role (e.g. "the platform team lead") and add a link to `team_and_stakeholders/contacts.md` or `ownership-map.md`. If identity isn't needed at all: drop the name entirely.

2. **Private-knowledge check** — Does the content contain any of the following? If yes, REMOVE before writing:
   - The string `private_knowledge`
   - Any path starting with `knowledge/private_knowledge/`
   - Any link/citation/embed pointing into `private_knowledge/`
   - Any inline content lifted from `private_knowledge/` raw files

3. **If either check fails** — Stop, redact, re-verify. Do not write a file that violates either rule.

These rules are inherited from the Kipi persona (`_bmad/kipi/SKILL.md`).

---

## Completion

When all chapters are written:

1. Run validation checklist
2. Update index.md with final stats
3. Set workflow state to completed
4. Show completion report with coverage summary

---

## Error Handling

- **File read error:** Log, skip file, continue
- **Chapter write error:** Save progress, ask user
- **Timeout/interrupt:** State file enables resume
- **Context overflow:** Reduce batch size, continue

