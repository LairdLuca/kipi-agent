# Step 1: Process User Data Files

## MANDATORY EXECUTION RULES (READ FIRST)

- 🛑 NEVER process files without user approval of file list
- ✅ ALWAYS check processed-files.md to avoid re-processing
- 📋 USE processed-file-template.md for all outputs
- 🔄 UPDATE workflow-state.json after EACH file processed
- 🧹 PURGE file contents after processing each file
- 💾 NEVER delete original before archive is confirmed

---

## Your Task

Process all new files in user-data/ folder, transforming them into structured documents and moving originals to archive.

---

## Execution Sequence

### Part 1: Identify New Files

#### 1.1 Setup Folders

Check and create if needed:
```
- {project-root}/user-data/           (input)
- {project-root}/ready-for-classify/  (output)
- {project-root}/_archive/            (archive)
```

#### 1.2 Load Processed Files Log

Read `{project-root}/_bmad/_memory/kipi-sidecar/processed-files.md`

Extract list of already-processed files.

#### 1.3 Scan user-data/

List all files in user-data/ folder.

Include subdirectories if present.

#### 1.4 Identify New Files

New files = files in user-data/ NOT in processed-files.md

#### 1.5 Present File List

```
📂 **Files Found in user-data/**

**New files to process:** {{new_count}}

{{#each new_files}}
{{@index}}. `{{filename}}` ({{size}})
{{/each}}

**Already processed:** {{processed_count}} files (will skip)

---

[P] Process all {{new_count}} files
[S] Select specific files to process
[C] Cancel and return to menu
```

#### 1.6 Initialize State

```json
{
  "workflow_type": "process-user-data",
  "timestamps": {"started": "{{now}}", "last_updated": "{{now}}"},
  "current_step": "processing",
  "user_data_processing": {
    "files_to_process": ["file1.md", "file2.md"],
    "files_processed": [],
    "files_failed": []
  }
}
```

---

### Part 2: Process Each File

For each file in files_to_process:

#### 2.1 Read File

```
Processing: {{filename}}
```

Load file contents into context.

#### 2.2 Detect Content Type

Analyze content for type indicators:

**Transcript Indicators:**
- Speaker labels (e.g., "John:", "[Speaker 1]")
- Timestamps in content
- Conversation format

**Meeting Notes Indicators:**
- "Attendees:" or "Participants:"
- "Agenda:" or "Topics:"
- "Action Items:" or "Next Steps:"
- Date in title/content

**Technical Notes Indicators:**
- Code blocks or snippets
- Technical terminology
- Architecture/design content
- API references

**Research Indicators:**
- Citations or references
- Analysis sections
- Findings/conclusions
- Data tables

**Log Indicators:**
- Timestamp prefixes
- Log levels (INFO, ERROR, DEBUG)
- Structured/repetitive format

If unclear, ask user:
```
🤔 **Content Type Unclear**

File: {{filename}}

Preview:
```
{{first_500_chars}}
```

What type is this content?
[1] Transcript
[2] Meeting Notes
[3] Technical Notes
[4] Research
[5] Logs
[6] General/Other
```

#### 2.3 Transform Content

Load `processed-file-template.md`

Create structured document:

**Part 1: Summary**
- Write 1-3 sentence summary of content
- Capture the essence/purpose

**Part 2: Key Information**
- Extract key points
- Extract action items (if present)
- Extract decisions (if present)
- Create overview paragraph

**Part 3: Organized Content**
- Divide content into logical sections
- Add section headings
- Maintain all original information
- Improve readability

**Part 4: Metadata**
- Original filename
- Content type
- Processing date
- Suggested Core-Knowledge chapter

#### 2.4 Save Processed File

Determine output path:
```
ready-for-classify/{content-type}/{original-filename}
```

Create subdirectory if needed.

Write processed file.

#### 2.5 Archive Original

Move original to:
```
_archive/{original-filename}
```

**CRITICAL:** Only archive AFTER processed file confirmed saved.

#### 2.6 Update processed-files.md

Add entry:
```markdown
| {{date}} | {{original_filename}} | {{content_type}} | {{output_path}} | {{archive_path}} |
```

#### 2.7 Update State

```json
{
  "files_processed": [
    {
      "original_path": "user-data/{{filename}}",
      "content_type": "{{type}}",
      "output_path": "ready-for-classify/{{type}}/{{filename}}",
      "archive_path": "_archive/{{filename}}",
      "timestamp": "{{now}}"
    }
  ]
}
```

#### 2.8 Purge & Report

🧹 Remove file contents from context.

Show result:
```
✓ Processed: {{filename}}
  Type: {{content_type}}
  Output: {{output_path}}
  Archived: {{archive_path}}

[N] Next file ({{remaining}} remaining)
[V] View processed file
[S] Stop processing
```

#### 2.9 Continue or Stop

If [N]: Continue to next file
If [V]: Show processed content, then continue
If [S]: Save state, return to menu (can resume later)

---

### Part 3: Complete

After all files processed:

```
✅ **Processing Complete!**

**Summary:**
- Files processed: {{processed_count}}
- Files failed: {{failed_count}}

**By Content Type:**
{{#each type_counts}}
- {{type}}: {{count}} files
{{/each}}

**Output Locations:**
{{#each output_folders}}
- {{folder}}: {{count}} files
{{/each}}

**Failed Files (if any):**
{{#each files_failed}}
- {{filename}}: {{error}}
{{/each}}

---

**Next Steps:**

The processed files are in `ready-for-classify/` organized by type.

To integrate into Core-Knowledge:
1. Review the processed files
2. Run [UK] Update Knowledge to merge relevant content
3. Or manually move files to appropriate chapters

[Done] Return to menu
```

---

## State Update (Final)

```json
{
  "current_step": "completed",
  "timestamps": {
    "completed": "{{now}}"
  },
  "user_data_processing": {
    "files_to_process": [...],
    "files_processed": [...],
    "files_failed": [...]
  }
}
```

---

## Error Handling

### Unreadable File
```
⚠️ Could not read: {{filename}}
Error: {{error_message}}

[S] Skip this file
[R] Retry
[A] Abort processing
```

Log to files_failed, continue if [S].

### Processing Error
- Don't archive original
- Log error to files_failed
- Show error to user
- Continue to next file

### Archive Failure
- Keep processed file
- Keep original in user-data/
- Log warning
- Continue (user can manually archive)

---

## Success Metrics

✅ All new files identified correctly
✅ Each file processed using template
✅ Content type correctly detected
✅ Originals archived safely
✅ processed-files.md updated
✅ Context purged after each file
✅ No data loss

---

## Failure Modes

❌ Processing already-processed files
❌ Archiving before confirming processed file saved
❌ Leaving template placeholders in output
❌ Not updating processed-files.md
❌ Accumulating file contents in context

---

## Validation

Before marking complete, verify:

- [ ] All processed files exist in ready-for-classify/
- [ ] All originals exist in _archive/
- [ ] processed-files.md has entry for each
- [ ] No files left in user-data/ that should have been processed
- [ ] No {{placeholder}} text in any output

Load checklist: `{agent-root}/checklists/process-user-data-checklist.md`

