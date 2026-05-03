# Code Scan Document Workflow — Validation Checklist

**Workflow:** `code-scan-document`
**Purpose:** Ensure comprehensive, high-quality code documentation

---

## Pre-Scan Checks

- [ ] Core-Knowledge structure exists (run repository-initialize first if not)
- [ ] index.md exists and lists planned chapters
- [ ] Sidecar files accessible
- [ ] Confirmed scan scope with user (full repo vs specific areas)

---

## Phase 1: Initial Mapping

### Mapping Completeness
- [ ] All major directories identified
- [ ] Entry points located
- [ ] Key configuration files found
- [ ] Test locations identified
- [ ] Build/deployment configs found
- [ ] Dependencies analyzed (package.json, requirements.txt, etc.)

### Mapping Output
- [ ] Project overview documented
- [ ] Directory tree with annotations
- [ ] Technology stack summary
- [ ] Architecture pattern identified

### State Management
- [ ] workflow-state.json updated
- [ ] step = "phase_1_mapping"
- [ ] ⏸️ STOPPED for user review and approval

---

## Phase 2: Chapter Planning

### For Each Chapter

- [ ] Clear scope defined (what's included/excluded)
- [ ] Source locations identified (which files/folders)
- [ ] Key topics listed
- [ ] Challenges/complexities noted
- [ ] Sub-chapter structure planned
- [ ] ⏸️ STOPPED for user approval on chapter plan

### Planning Quality
- [ ] No overlapping content between chapters
- [ ] All major code areas covered
- [ ] Priority order makes sense
- [ ] Estimated complexity noted

### State Management
- [ ] chapters_progress array populated
- [ ] Each chapter has entry with status = "planned"

---

## Phase 3: Writing (Per Chapter)

### Template Usage
- [ ] chapter-template.md loaded
- [ ] All template sections present in output
- [ ] No template placeholders remain ({{variable}})

### Chapter Structure (MANDATORY)
- [ ] ✅ Summary at top (1-3 sentences MAX)
- [ ] ✅ Table of Contents with all sub-chapters
- [ ] ✅ Sub-chapters with clear headings
- [ ] ✅ Code references with file paths
- [ ] ✅ Change History section at bottom

### Content Quality
- [ ] Summary is concise and accurate
- [ ] TOC matches actual sub-chapter headings
- [ ] Sub-chapters have substantial content
- [ ] Code references point to real, existing files
- [ ] No placeholder text remains
- [ ] Technical accuracy verified
- [ ] Depth appropriate (not just summaries)

### Batching (for large chapters)
- [ ] Files processed by subfolder batches
- [ ] Each batch: read → extract → write → purge
- [ ] Batch completion logged to workflow-state.json
- [ ] Context purged after each batch
- [ ] Only summaries kept in memory

### State Management (per chapter)
- [ ] chapters_progress[chapter].status = "in_progress" during writing
- [ ] chapters_progress[chapter].status = "written" after completion
- [ ] batches_completed updated for each batch
- [ ] outputs_generated updated

---

## Post-Writing Validation

### Chapter Validation
- [ ] All planned chapters written
- [ ] Each chapter passes structure check (Summary/TOC/Content)
- [ ] No chapters with placeholder-only content
- [ ] File size reasonable (not empty, not excessively large)

### Cross-Reference Validation
- [ ] All code file references exist
- [ ] All internal links between chapters work
- [ ] Related chapters properly cross-referenced

### Index Update
- [ ] index.md updated with all written chapters
- [ ] Chapter summaries in index are accurate
- [ ] Quick Stats updated (chapter count, etc.)
- [ ] No "_(To be generated)_" markers for completed chapters

---

## Content Quality Checks

### Depth Check
- [ ] Chapters provide deep knowledge, not just summaries
- [ ] Implementation details included
- [ ] Patterns and conventions documented
- [ ] Edge cases and gotchas noted
- [ ] Enough detail for someone unfamiliar with codebase

### Accuracy Check
- [ ] File paths are correct
- [ ] Code examples match actual code
- [ ] Technology versions accurate
- [ ] No outdated information

### Usability Check
- [ ] Easy to navigate via TOC
- [ ] Sub-chapters are focused
- [ ] Code locations clearly marked
- [ ] Searchable for key terms

---

## State File Validation

- [ ] workflow-state.json is valid JSON
- [ ] All completed_steps have timestamps
- [ ] All chapters in chapters_progress have status
- [ ] outputs_generated is complete
- [ ] resume_instructions are clear

---

## Completion Criteria

All of the following must be true:
1. ✅ All planned chapters written
2. ✅ Each chapter has Summary, TOC, Sub-chapters
3. ✅ All code references point to existing files
4. ✅ No placeholder text in any output
5. ✅ index.md accurately reflects content
6. ✅ workflow-state.json shows completed
7. ✅ Context properly purged (no memory bloat)

---

## Common Issues

### Critical Issues (Must Fix)
- [ ] ❌ Missing chapters from plan
- [ ] ❌ Chapters without proper structure
- [ ] ❌ Broken file references
- [ ] ❌ Placeholder text remaining

### Quality Issues (Should Fix)
- [ ] ⚠️ Summaries too long (should be 1-3 sentences)
- [ ] ⚠️ TOC doesn't match actual headings
- [ ] ⚠️ Shallow content (summaries instead of depth)
- [ ] ⚠️ Missing cross-references

---

## Sign-Off

- [ ] All checklist items verified
- [ ] User reviewed and approved documentation
- [ ] Ready for use / maintenance mode

