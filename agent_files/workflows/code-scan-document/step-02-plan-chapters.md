# Step 2: Plan Chapters

## MANDATORY EXECUTION RULES (READ FIRST)

- 🛑 NEVER start writing without chapter plan approval (unless auto_approve mode)
- ✅ ALWAYS plan ONE chapter at a time with user review
- 📋 YOU ARE PLANNING CONTENT, not writing yet
- 🔄 UPDATE workflow-state.json after each chapter planned
- ⏸️ STOP after each chapter plan for user approval (unless auto_approve = true)
- 🤖 If auto_approve mode: Still present plan, but auto-approve and continue

---

## Prerequisites

- Step 1 (Mapping) must be completed
- workflow-state.json must have findings.project_mapping
- User must have approved the mapping

---

## Your Task

For each chapter identified in the mapping, create a detailed content plan that will guide the writing phase. Each plan defines what the chapter will contain, what sub-chapters it will have, and which files will be documented.

---

## Execution Sequence

### 1. Load Chapter List

From workflow-state.json, get:
- `findings.project_mapping.chapter_mappings`
- `scan_level`
- `approval_mode` or `auto_approve` (default: false)

**If approval_mode not set:**
- Ask user: "Choose approval mode: [I]nteractive (approve each chapter) or [A]uto-approve (create all automatically)?"
- Set `approval_mode` in workflow-state.json based on response
- If auto-approve: Set `auto_approve: true`

Order chapters by:
1. Priority from index.md
2. Dependency (overview before details)
3. Complexity (simpler first)

### 2. Plan Each Chapter (Loop)

For each chapter, execute this planning cycle:

#### A. Analyze Chapter Scope

Based on chapter name and mapped sources:
- What is this chapter's purpose?
- What questions should it answer?
- Who is the target reader?

#### B. Define Sub-Chapters

Create 3-7 sub-chapters that:
- Cover the chapter scope comprehensively
- Follow logical reading order
- Are independently useful

Example for "Architecture" chapter:
```
Sub-chapters:
1. System Overview
2. Core Components
3. Data Flow
4. Key Design Decisions
5. Dependencies & Integrations
6. Deployment Architecture
```

#### C. Map Sub-Chapters to Sources

For each sub-chapter:
```yaml
sub_chapter: "System Overview"
source_files:
  - src/app.ts
  - src/config/index.ts
source_directories:
  - src/core/
key_elements:
  - "Main application bootstrap"
  - "Configuration loading"
  - "Dependency injection setup"
```

#### D. Estimate Effort

Based on scan_level and sources:
- Quick: Pattern-based summary
- Chapter: Read key files only
- Exhaustive: Read all files

Estimate batches needed:
```
Chapter "Architecture":
- 3 subfolders to scan
- ~45 files total
- Estimated: 3 batches
```

#### E. Present Chapter Plan

```
📋 **Chapter Plan: {{chapter_name}}**

**Purpose:** {{chapter_purpose}}

**Target Reader:** {{reader_description}}

**Sub-chapters:**

1. **{{sub_chapter_1}}**
   - Sources: `{{source_files}}`
   - Key elements: {{elements_list}}

2. **{{sub_chapter_2}}**
   - Sources: `{{source_files}}`
   - Key elements: {{elements_list}}

[... continue for each sub-chapter ...]

**Scan Strategy:**
- Directories to scan: {{directory_count}}
- Files to read: {{file_count}}
- Estimated batches: {{batch_count}}

---

[A] Approve this chapter plan
[E] Edit - suggest changes
[S] Skip this chapter for now
[C] Continue to next chapter
```

#### F. Handle Response

**If auto_approve = true:**
1. Present the chapter plan (as shown above)
2. Show: "🤖 Auto-approve mode: Automatically approving and continuing..."
3. Update workflow-state.json with chapter plan
4. Set chapter status = "planned"
5. Continue to next chapter immediately
6. **Still allow user to interrupt:** If user types [E] Edit or [S] Skip, handle normally

**If [A] Approve (interactive mode):**
1. Update workflow-state.json with chapter plan
2. Set chapter status = "planned"
3. Continue to next chapter

**If [E] Edit:**
1. Ask what needs changing
2. Modify plan
3. Re-present

**If [S] Skip:**
1. Mark chapter as "skipped"
2. Continue to next chapter

**If [C] Continue (after all chapters):**
1. Proceed to step-03-write.md

### 3. Planning Summary

After all chapters planned:

```
✅ **Chapter Planning Complete**

**Planned:** {{planned_count}} chapters
**Skipped:** {{skipped_count}} chapters

**Writing Order:**
1. {{chapter_1}} ({{batch_count}} batches)
2. {{chapter_2}} ({{batch_count}} batches)
...

**Total Estimated:**
- Files to scan: {{total_files}}
- Batches: {{total_batches}}
- Time: {{time_estimate}}

---

[W] Begin writing chapters
[R] Review/modify plans
{{#if auto_approve}}
[I] Switch to Interactive mode (approve each chapter)
{{else}}
[A] Switch to Auto-approve mode (create all automatically)
{{/if}}
```

---

## State Update (Per Chapter)

After each chapter plan approval:

```json
{
  "findings": {
    "chapters_progress": [
      {
        "chapter_name": "Architecture",
        "status": "planned",
        "file_path": "knowledge/core_knowledge/architecture.md",
        "sub_chapters": [
          {
            "name": "System Overview",
            "sources": ["src/app.ts", "src/core/"],
            "key_elements": ["bootstrap", "config"]
          }
        ],
        "estimated_batches": 3,
        "summary": null
      }
    ]
  }
}
```

---

## State Update (After All Planning)

```json
{
  "current_step": "step_2_plan",
  "completed_steps": [
    {"step": "step_1_map", "status": "completed", ...},
    {"step": "step_2_plan", "status": "completed", "timestamp": "...",
     "summary": "Planned {{count}} chapters, {{batches}} total batches"}
  ],
  "resume_instructions": "All chapters planned. Ready to write."
}
```

---

## Success Metrics

✅ Each chapter has clear purpose defined
✅ Each chapter has 3-7 sub-chapters
✅ Each sub-chapter mapped to sources
✅ Batch estimates calculated
✅ User approved each chapter plan
✅ Writing order established

---

## Failure Modes

❌ Planning all chapters without user review
❌ Sub-chapters without source mappings
❌ Too many or too few sub-chapters
❌ Not estimating batches
❌ Proceeding to write without plan approval

---

## Next Step

After user selects [W] to begin writing:

1. Verify all intended chapters are planned
2. Update current_step = "step_3_write"
3. Load and execute `./step-03-write.md`

**DO NOT proceed until user explicitly approves all plans!**

