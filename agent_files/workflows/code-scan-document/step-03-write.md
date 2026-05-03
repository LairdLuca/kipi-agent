# Step 3: Write Chapters

## MANDATORY EXECUTION RULES (READ FIRST)

- 🛑 NEVER write without using chapter-template.md
- ✅ ALWAYS write in batches with immediate disk writes
- 📋 USE the planned sub-chapters from Step 2
- 🔄 UPDATE workflow-state.json after EACH batch
- 🧹 PURGE detailed content from context after each batch
- ⏸️ STOP after each chapter for user review (unless auto_approve = true)
- 🤖 If auto_approve mode: Still show completion summary, but auto-continue to next chapter

---

## Prerequisites

- Step 2 (Planning) must be completed
- All intended chapters must have status = "planned"
- Chapter plans must include sub-chapters and source mappings
- Load `approval_mode` or `auto_approve` from workflow-state.json (default: false)

---

## Your Task

Write each planned chapter using the batched, write-as-you-go approach. Generate deep, useful documentation following the chapter template.

---

## Writing Process (Per Chapter)

### For Each Planned Chapter:

#### Phase A: Initialize Chapter File

1. Load chapter-template.md
2. Create initial file with:
   - Chapter title
   - Empty summary (will fill after content)
   - TOC placeholder
   - Sub-chapter headings

```markdown
# {{chapter_name}}

> **Summary:** _Writing in progress..._

**Last Updated:** {{date}}
**Status:** 🔄 In Progress

---

## Table of Contents

_Updating as content is written..._

---

{{#each sub_chapters}}
## {{name}}

_Content pending..._

---
{{/each}}
```

3. Save initial file to disk
4. Update state: chapter status = "in_progress"

#### Phase B: Write Sub-Chapters (Batched)

For each sub-chapter in the plan:

**1. Identify Batch Scope**
```
Sub-chapter: "System Overview"
Sources: src/app.ts, src/config/
Files in batch: 8
```

**2. Read Source Files**

Based on scan_level:
- **Quick:** Use file names, comments, exports only
- **Chapter:** Read files in mapped sources
- **Exhaustive:** Read all files in directories

**3. Extract Information**

For each file:
- Purpose/role in the system
- Key exports/functions/classes
- Dependencies
- Important patterns
- Notable comments/TODOs

**4. Write Sub-Chapter Content**

Generate content that answers:
- What does this code do?
- How does it work?
- Why is it structured this way?
- What are the key files?
- What should someone know to modify this?

**5. IMMEDIATELY Append to Chapter File**

Replace the "_Content pending..._" placeholder with actual content.

**6. Update State**

```json
{
  "chapters_progress": [{
    "chapter_name": "Architecture",
    "status": "in_progress",
    "batches_completed": [
      {"path": "src/core/", "files": 8, "summary": "Core bootstrap"}
    ],
    "sub_chapters_written": 1
  }],
  "batches_completed": [
    {"path": "src/core/", "files_scanned": 8, "summary": "Core bootstrap code"}
  ]
}
```

**7. PURGE Batch Data**

Remove from context:
- File contents
- Detailed analysis

Keep only:
- Summary: "src/core/ - 8 files - Core bootstrap and DI"

**8. Continue to Next Sub-Chapter**

Repeat for each sub-chapter in the chapter.

#### Phase C: Finalize Chapter

After all sub-chapters written:

**1. Write Summary**

Read the completed chapter and write a 1-3 sentence summary at the top.

**2. Update TOC**

Replace TOC placeholder with actual headings:

```markdown
## Table of Contents

- [System Overview](#system-overview)
- [Core Components](#core-components)
- [Data Flow](#data-flow)
...
```

**3. Add Code References**

At the bottom, list all files referenced:

```markdown
## Files Referenced

- `src/app.ts`
- `src/config/index.ts`
- `src/core/bootstrap.ts`
...
```

**4. Add Change History**

```markdown
## Change History

| Date | Change | Author |
|------|--------|--------|
| {{date}} | Initial documentation | Kipi |
```

**5. Validate Structure**

Check chapter has:
- [ ] Summary (1-3 sentences)
- [ ] TOC matching headings
- [ ] All planned sub-chapters
- [ ] Code references section
- [ ] Change history

**6. Update State**

```json
{
  "chapters_progress": [{
    "chapter_name": "Architecture",
    "status": "written",
    "sub_chapters_count": 5,
    "summary": "System architecture covering bootstrap, core components, and data flow"
  }]
}
```

#### Phase D: User Review

After each chapter:

```
✅ **Chapter Complete: {{chapter_name}}**

**Summary:** {{chapter_summary}}

**Sub-chapters Written:**
{{#each sub_chapters}}
- {{name}} ✓
{{/each}}

**Files Documented:** {{file_count}}
**Batches Processed:** {{batch_count}}

---

{{#if auto_approve}}
🤖 **Auto-approve mode:** Automatically continuing to next chapter...
(You can still interrupt with [E] Edit, [V] View, or [F] Finish)
{{else}}
[V] View the chapter
[N] Continue to next chapter
[E] Edit this chapter
[F] Finish writing (skip remaining)
[A] Switch to Auto-approve mode (create remaining automatically)
{{/if}}
```

**If auto_approve = true:**
1. Show the completion summary above
2. Wait 2-3 seconds (or check if user interrupted)
3. If no interruption: Automatically continue to next chapter
4. If user types [E], [V], or [F]: Handle normally (pause auto-approve for this action)

**If auto_approve = false (interactive mode):**
1. Show the completion summary
2. Wait for user input
3. Handle [V], [N], [E], [F] as normal

---

## State Management (During Writing)

Update workflow-state.json frequently:

```json
{
  "current_step": "step_3_write",
  "current_chapter": "architecture",
  "current_sub_chapter": 3,
  "chapters_progress": [...],
  "batches_completed": [...],
  "outputs_generated": [
    "knowledge/core_knowledge/overview.md",
    "knowledge/core_knowledge/architecture.md"
  ],
  "resume_instructions": "Writing architecture chapter, sub-chapter 3 of 5"
}
```

---

## Index Update

After each chapter completes:

1. Open index.md
2. Update chapter entry:
   ```markdown
   ### 2. [Architecture](./architecture.md)

   > System architecture covering bootstrap, core components, and data flow.

   **Status:** ✅ Written | **Sub-chapters:** 5
   ```
3. Save index.md

---

## Completion (All Chapters)

After all planned chapters are written:

```
🎉 **Code Scan Documentation Complete!**

**Chapters Written:** {{written_count}}/{{planned_count}}
**Sub-chapters Total:** {{sub_chapter_total}}
**Files Documented:** {{files_total}}

**Chapter Summary:**
{{#each written_chapters}}
- **{{name}}:** {{summary}}
{{/each}}

**Quick Stats:**
- Total batches processed: {{batch_total}}
- Context purges: {{purge_count}}
- Time elapsed: {{duration}}

**Index updated:** knowledge/core_knowledge/index.md

---

**Next Steps:**
- [ST] View status and coverage
- [UK] Update knowledge after code changes
- [CL] Run cleanup/validation

[Done] Return to menu
```

---

## Validation

Before marking workflow complete:

Load checklist: `{agent-root}/checklists/code-scan-checklist.md`

Verify:
- [ ] All planned chapters written
- [ ] Each chapter has Summary, TOC, Content
- [ ] All code references exist
- [ ] index.md updated
- [ ] No placeholder text remains

---

## Success Metrics

✅ All planned chapters written
✅ Each chapter follows template structure
✅ Summary is 1-3 sentences
✅ TOC matches actual headings
✅ Code references are accurate
✅ Context properly managed (no overflow)
✅ User reviewed each chapter

---

## Failure Modes

❌ Not using chapter-template.md
❌ Writing entire chapter in memory (will overflow)
❌ Not purging context after batches
❌ Placeholder text remaining
❌ Broken code references
❌ Skipping user review

---

## Context Management Example

**Before batch:**
```
Context: [chapter plan, previous summaries]
```

**During batch:**
```
Context: [chapter plan, batch files (large), previous summaries]
```

**After batch:**
```
Context: [chapter plan, previous summaries, new 1-line summary]
Files written: Chapter updated on disk
Purged: All batch file contents
```

This allows processing unlimited codebase size.

---

## Workflow Complete

After user selects [Done]:

1. Set workflow state to completed
2. Clear any remaining context
3. Return to Kipi menu
4. Workflow state preserved for reference/resume

