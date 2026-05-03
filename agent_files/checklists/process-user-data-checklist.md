# Process User Data Workflow — Validation Checklist

**Workflow:** `process-user-data`
**Purpose:** Ensure proper processing of files in user-data/ folder

---

## Pre-Processing Checks

- [ ] user-data/ folder exists
- [ ] processed-files.md exists and is readable
- [ ] _archive/ folder exists (create if not)
- [ ] ready-for-classify/ folder exists (create if not)
- [ ] Sidecar files accessible

---

## Step 1: File Identification

### Scan Completeness
- [ ] Scanned all files in user-data/
- [ ] Checked subdirectories if present
- [ ] Cross-referenced with processed-files.md
- [ ] Identified truly new files only
- [ ] Excluded already-processed files

### File List Output
- [ ] Listed all new files to process
- [ ] Showed file names and sizes
- [ ] Identified file types where obvious
- [ ] Count of new files clear

### User Interaction
- [ ] ⏸️ STOPPED to show file list
- [ ] Got user approval to proceed
- [ ] If no files: informed user and exited gracefully

### State Management
- [ ] workflow-state.json initialized
- [ ] workflow_type = "process-user-data"
- [ ] user_data_processing.files_to_process populated

---

## Step 2: Per-File Processing

### For Each File

#### Content Analysis
- [ ] File successfully read
- [ ] Content type identified (transcript, log, notes, image, etc.)
- [ ] Key topics/themes extracted
- [ ] Structure of original content understood

#### Template Usage
- [ ] processed-file-template.md loaded
- [ ] All template sections present
- [ ] No {{variable}} placeholders remain

#### Document Structure (MANDATORY)
- [ ] ✅ Summary at top (concise, 1-3 sentences)
- [ ] ✅ Table of Contents with sections
- [ ] ✅ Overview with key points
- [ ] ✅ Full content organized by sections
- [ ] ✅ Metadata section at bottom

#### Content Quality
- [ ] Summary captures essence of content
- [ ] TOC matches actual sections
- [ ] Key points accurately extracted
- [ ] Action items captured (if present)
- [ ] Decisions captured (if present)
- [ ] Content properly organized
- [ ] Classification suggestion provided

#### File Operations
- [ ] Processed file saved to ready-for-classify/{content-type}/
- [ ] Original file moved to _archive/
- [ ] processed-files.md updated with entry
- [ ] Entry includes: original path, timestamp, output path

#### State Management
- [ ] files_processed entry added
- [ ] Entry includes: original_path, content_type, output_path, archive_path
- [ ] ⏸️ STOPPED after each file to show result

#### Context Management
- [ ] File contents purged from context after processing
- [ ] Only summary retained: "{filename} → {content_type}"

---

## processed-files.md Update

### Entry Quality
- [ ] Each entry has: original filename
- [ ] Each entry has: processing timestamp
- [ ] Each entry has: output file path
- [ ] Each entry has: archive path
- [ ] Each entry has: content type identified

### File Integrity
- [ ] No duplicate entries
- [ ] Chronological order maintained
- [ ] Format consistent with existing entries

---

## ready-for-classify/ Organization

### Folder Structure
- [ ] Subfolders by content type (transcripts/, logs/, notes/, etc.)
- [ ] Files placed in correct type folder
- [ ] Filenames are clear and consistent

### File Quality
- [ ] Each file is valid Markdown
- [ ] No broken internal references
- [ ] Renders correctly

---

## _archive/ Organization

### Archive Quality
- [ ] Original files preserved intact
- [ ] File timestamps preserved where possible
- [ ] Organization allows retrieval if needed

---

## Error Handling

### Per-File Errors
- [ ] If file can't be read: logged to files_failed
- [ ] If type unknown: asked user for guidance
- [ ] If processing fails: original NOT deleted from user-data/
- [ ] Error messages are clear and actionable

### Recovery
- [ ] Partial progress saved to workflow-state.json
- [ ] Can resume from last successful file
- [ ] files_failed list is accurate

---

## Post-Processing Validation

### Completeness Check
- [ ] All files_to_process either in files_processed or files_failed
- [ ] No files left in user-data/ that should have been processed
- [ ] processed-files.md has entry for each processed file

### Quality Check
- [ ] Spot-check 1-2 processed files for quality
- [ ] Summaries are accurate
- [ ] Content properly organized
- [ ] Classification suggestions reasonable

---

## Summary Report

### Report Contents
- [ ] Total files processed
- [ ] Files by content type
- [ ] Any failures noted
- [ ] Next steps suggested (classify into Core-Knowledge)

### Report Quality
- [ ] Concise and scannable
- [ ] Actionable information
- [ ] Clear success/failure indication

---

## State File Validation

- [ ] workflow-state.json is valid JSON
- [ ] files_to_process matches initial scan
- [ ] files_processed has all successes
- [ ] files_failed has all failures
- [ ] step = "completed"

---

## Completion Criteria

All of the following must be true:
1. ✅ All new files in user-data/ identified
2. ✅ Each file processed using template
3. ✅ Each processed file has Summary/TOC/Content
4. ✅ Originals archived to _archive/
5. ✅ Outputs saved to ready-for-classify/
6. ✅ processed-files.md updated completely
7. ✅ No files lost or corrupted

---

## Common Issues

### Critical Issues (Must Fix)
- [ ] ❌ Original file deleted before archive confirmed
- [ ] ❌ processed-files.md not updated
- [ ] ❌ File processed but not in ready-for-classify/

### Quality Issues (Should Fix)
- [ ] ⚠️ Poor content type classification
- [ ] ⚠️ Summary doesn't capture key points
- [ ] ⚠️ Missing action items or decisions

---

## Sign-Off

- [ ] All checklist items verified
- [ ] User confirmed processing complete
- [ ] Ready for classification into Core-Knowledge

