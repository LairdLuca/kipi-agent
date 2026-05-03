# Process User Data Workflow

**Workflow ID:** `process-user-data`
**Purpose:** Process files in user-data/ folder into structured documents
**Menu Trigger:** `[UD]` or fuzzy match on `user-data`

---

## Overview

This workflow processes new files dropped into the user-data/ folder, transforming them into structured, classified documents ready for integration into Core-Knowledge.

---

## Configuration

```yaml
workflow_type: process-user-data
state_file: {project-root}/_bmad/_memory/kipi-sidecar/workflow-state.json
input_folder: {project-root}/user-data/
output_folder: {project-root}/ready-for-classify/
archive_folder: {project-root}/_archive/
tracking_file: {project-root}/_bmad/_memory/kipi-sidecar/processed-files.md
templates:
  processed_file: {agent-root}/templates/processed-file-template.md
checklist: {agent-root}/checklists/process-user-data-checklist.md
```

---

## Prerequisites

Before starting:
1. user-data/ folder must exist (create if not)
2. processed-files.md must be accessible
3. ready-for-classify/ folder must exist (create if not)
4. _archive/ folder must exist (create if not)

---

## Execution Steps

### Step 1: Identify New Files
**File:** `./step-01-process.md`

- Scan user-data/ for all files
- Cross-reference with processed-files.md
- List only truly new files
- Present list for user approval
- ⏸️ STOP for user to confirm processing

### Step 2: Process Each File (Loop)

For each new file:

1. **Read & Analyze**
   - Load file contents
   - Identify content type (transcript, notes, log, etc.)
   - Extract key information

2. **Transform**
   - Load processed-file-template.md
   - Create structured 3-part document:
     - Summary
     - Table of Contents
     - Full organized content

3. **Save & Archive**
   - Save to ready-for-classify/{content-type}/
   - Move original to _archive/
   - Update processed-files.md

4. **Update State**
   - Mark file as processed
   - Purge file contents from context
   - ⏸️ STOP to show result

### Step 3: Complete

- Generate processing summary
- Offer to classify into Core-Knowledge
- Return to menu

---

## Content Type Detection

Automatically detect type based on content patterns:

| Content Type | Indicators | Output Folder |
|--------------|------------|---------------|
| `transcript` | Speaker labels, timestamps | ready-for-classify/transcripts/ |
| `meeting-notes` | Attendees, agenda, action items | ready-for-classify/meetings/ |
| `technical-notes` | Code snippets, technical terms | ready-for-classify/technical/ |
| `research` | Citations, analysis, findings | ready-for-classify/research/ |
| `logs` | Timestamps, log levels, structured data | ready-for-classify/logs/ |
| `general` | Unclassified content | ready-for-classify/general/ |

---

## State Management

### Initialization
```json
{
  "workflow_type": "process-user-data",
  "timestamps": {"started": "...", "last_updated": "..."},
  "current_step": "step_1_identify",
  "user_data_processing": {
    "files_to_process": [],
    "files_processed": [],
    "files_failed": []
  }
}
```

### Per-File Tracking
```json
{
  "files_processed": [
    {
      "original_path": "user-data/meeting-2026-01-25.md",
      "content_type": "meeting-notes",
      "output_path": "ready-for-classify/meetings/meeting-2026-01-25.md",
      "archive_path": "_archive/meeting-2026-01-25.md",
      "timestamp": "2026-01-25T10:30:00Z"
    }
  ]
}
```

---

## processed-files.md Format

```markdown
# Processed Files Log

| Date | Original File | Type | Output Location | Archive |
|------|---------------|------|-----------------|---------|
| 2026-01-25 | meeting-notes.md | meeting | ready-for-classify/meetings/... | _archive/... |
```

---

## Document Structure (Output)

Every processed file follows this structure:

```markdown
# {{original_filename}}

> **Summary:** {{one_line_summary}}

**Source:** `{{original_path}}`
**Type:** {{content_type}}
**Processed:** {{date}}

---

## Table of Contents

- [Overview](#overview)
- [Key Points](#key-points)
- [Full Content](#full-content)

---

## Overview

{{overview_paragraph}}

### Key Points

- Point 1
- Point 2
...

### Action Items (if applicable)

- [ ] Action 1
- [ ] Action 2

---

## Full Content

### Section 1

{{organized_content}}

### Section 2

{{organized_content}}

---

## Metadata

| Field | Value |
|-------|-------|
| Original | {{filename}} |
| Type | {{content_type}} |
| Processed | {{date}} |

### Classification Suggestion

**Suggested Chapter:** {{suggested_chapter}}
**Confidence:** {{percentage}}%
```

---

## Hard Rules (CRITICAL — applied to every output destined for `core_knowledge/`)

Raw files arriving in `user-data/` may contain names, contact info, private context, and references to internal sources. When this workflow produces output destined for `core_knowledge/` (or for downstream classification into `core_knowledge/`), it MUST:

1. **Strip names from technical content** — Replace human first/last names with ROLES (e.g. "the on-call engineer", "the team lead"). Identity-bearing content (contact info, ownership, who-said-what) gets routed into `team_and_stakeholders/` instead.

2. **Never expose `private_knowledge/`** — If a raw file lives in or under `private_knowledge/`, the digested representation written into `core_knowledge/` must NOT:
   - Mention the path `private_knowledge/`
   - Link to or cite anything inside `private_knowledge/`
   - Quote `private_knowledge/` content verbatim

   The output must be self-contained: the same value, distilled into a name-free, role-based form.

3. **Self-check before writing** — Before saving any file that will land in `core_knowledge/`, scan the output for human names outside `team_and_stakeholders/` and any `private_knowledge` paths. Redact, reroute, or refuse to write if found.

4. **Raw files in `private_knowledge/raw_data/` are processed in place** — they are read, distilled, and the distillation is written elsewhere. The raw file itself stays in `private_knowledge/` and is never referenced from `core_knowledge/`.

These rules are inherited from the Kipi persona (`_bmad/kipi/SKILL.md`).

---

## Error Handling

- **Unreadable file:** Log to files_failed, continue
- **Unknown type:** Ask user to classify
- **Processing error:** Don't archive original, log error
- **Interrupt:** State enables resume

---

## Resumability

On activation, check for in-progress:

```
📋 Found in-progress processing:
- Files to process: 5
- Already processed: 2
- Current file: meeting-notes-2.md

[R] Resume processing
[S] Start fresh
```

---

## Completion

```
✅ **Processing Complete!**

**Processed:** {{count}} files
**By Type:**
- Transcripts: {{count}}
- Meeting Notes: {{count}}
- Technical: {{count}}

**Output Location:** ready-for-classify/

**Next Steps:**
- Review processed files
- Run [UK] to integrate into Core-Knowledge
- Or manually move to appropriate chapters

[Done] Return to menu
```

