# Step 1: Project Mapping

## MANDATORY EXECUTION RULES (READ FIRST)

- 🛑 NEVER proceed to planning without user approval of mapping
- ✅ ALWAYS verify Core-Knowledge structure exists first
- 📋 YOU ARE MAPPING, not writing content yet
- 🔄 UPDATE workflow-state.json after completing this step
- 📊 Keep mapping data concise for later steps

---

## Prerequisites Check

Before starting:
1. Verify `{project-root}/knowledge/core_knowledge/index.md` exists
2. If not: inform user to run [RI] Repository Initialize first
3. Load index.md to get list of chapters to populate

---

## Your Task

Create a comprehensive map of the repository that will guide chapter planning and writing. Focus on understanding the codebase structure and identifying what each chapter will document.

---

## Execution Sequence

### 1. Select Scan Level

Present options to user:

```
📊 **Select Scan Depth**

How thoroughly should I scan the codebase?

**[1] Quick Scan** (5-15 minutes)
- Pattern-based analysis only
- No source file reading
- Good for: Initial overview

**[2] Chapter Scan** (30-60 minutes) [RECOMMENDED]
- Read files relevant to each chapter
- Balanced depth and speed
- Good for: Standard documentation

**[3] Exhaustive Scan** (1-3 hours)
- Read ALL source files
- Maximum detail
- Good for: Deep audit, migration planning

Your choice [1/2/3]:
```

Store selection as `{{scan_level}}`

### 2. Scan Directory Structure

Generate complete directory tree:

```
{project_root}/
├── src/
│   ├── components/     # [count] files
│   ├── services/       # [count] files
│   ├── utils/          # [count] files
│   └── ...
├── tests/
│   └── ...
├── config/
│   └── ...
└── ...
```

For each directory, note:
- File count
- Primary file types
- Apparent purpose

### 3. Identify Entry Points

Locate and document:

**Application Entry Points:**
- main.ts / index.ts / app.ts
- server.ts / api.ts
- main.py / __main__.py
- main.go / cmd/

**Configuration Entry Points:**
- package.json scripts
- Makefile targets
- docker-compose.yml services

**Test Entry Points:**
- jest.config.js
- pytest.ini
- test patterns

### 4. Map to Chapters

For each chapter in index.md, identify:

```yaml
chapter: "Overview"
primary_sources:
  - README.md
  - package.json
  - docs/getting-started.md
related_directories: []
estimated_complexity: low

chapter: "Architecture"
primary_sources:
  - src/app.ts
  - src/config/
related_directories:
  - src/core/
  - src/infrastructure/
estimated_complexity: medium

# ... for each chapter
```

### 5. Identify Key Patterns

Document codebase patterns:

**File Organization:**
- Naming conventions (kebab-case, PascalCase, etc.)
- Module structure
- Test file locations

**Code Patterns:**
- Import/export style
- Configuration approach
- Error handling patterns

**Documentation Patterns:**
- Existing comments style
- JSDoc / docstrings presence
- README patterns

---

## State Update

After mapping completes:

```json
{
  "workflow_type": "code-scan-document",
  "timestamps": {
    "started": "{{now}}",
    "last_updated": "{{now}}"
  },
  "scan_level": "{{selected_level}}",
  "current_step": "step_1_map",
  "completed_steps": [],
  "findings": {
    "project_mapping": {
      "total_directories": {{count}},
      "total_files": {{count}},
      "entry_points": ["{{list}}"],
      "key_directories": [
        {"path": "src/", "purpose": "...", "file_count": 0}
      ],
      "chapter_mappings": [
        {
          "chapter": "Overview",
          "sources": ["..."],
          "complexity": "low"
        }
      ],
      "patterns_identified": ["..."]
    }
  },
  "resume_instructions": "Mapping complete. Ready to plan chapters."
}
```

---

## Present Results

Show user the mapping summary:

```
🗺️ **Project Mapping Complete**

**Codebase Stats:**
- Directories: {{directory_count}}
- Source Files: {{file_count}}
- Scan Level: {{scan_level}}

**Entry Points Identified:**
{{entry_points_list}}

**Key Directories:**
{{#each key_directories}}
- `{{path}}` — {{purpose}} ({{file_count}} files)
{{/each}}

**Chapter → Source Mapping:**

| Chapter | Primary Sources | Complexity |
|---------|-----------------|------------|
{{#each chapter_mappings}}
| {{chapter}} | {{sources_summary}} | {{complexity}} |
{{/each}}

**Patterns Observed:**
{{patterns_list}}

---

**Coverage Estimate:**
Based on this mapping, the {{scan_level}} scan will document approximately {{coverage_estimate}}% of the codebase.

---

[C] Continue to chapter planning
[A] Adjust mapping before continuing
```

---

## Handle User Response

### If [C] Continue:
1. Mark step_1_map as completed
2. Proceed to step-02-plan-chapters.md

### If [A] Adjust:
1. Ask what needs adjustment
2. Modify mapping
3. Re-present results

---

## Success Metrics

✅ Core-Knowledge structure verified
✅ Scan level selected by user
✅ Complete directory tree generated
✅ Entry points identified
✅ Each chapter mapped to sources
✅ Patterns documented
✅ User approved mapping

---

## Failure Modes

❌ Starting without Core-Knowledge structure
❌ Not offering scan level choice
❌ Missing major directories in mapping
❌ Chapters without source mappings
❌ Proceeding without user approval

---

## Next Step

After user selects [C] to continue:

1. Add to completed_steps: `{"step": "step_1_map", "status": "completed", ...}`
2. Update current_step = "step_2_plan"
3. Load and execute `./step-02-plan-chapters.md`

**DO NOT proceed until user explicitly continues!**

