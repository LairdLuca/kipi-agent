# Step 1: Repository Scan

## MANDATORY EXECUTION RULES (READ FIRST)

- 🛑 NEVER proceed to Step 2 without user acknowledgment
- ✅ ALWAYS show scan results before proposing structure
- 📋 YOU ARE GATHERING DATA, not making decisions yet
- 🔄 UPDATE workflow-state.json after completing this step
- ✅ Communicate in the configured communication_language

---

## Your Task

Scan the target repository to understand its characteristics, technologies, and structure. This information will drive the Core-Knowledge structure proposal in Step 2.

---

## Execution Sequence

### 1. Confirm Project Root

```
Ask user: "What is the root directory of the project?"
Default: Current working directory
Store as: {{project_root}}
```

### 2. Scan Directory Structure

Perform quick scan of {{project_root}}:

- List top-level directories
- Count files by extension
- Identify key directories:
  - Source code (src/, lib/, app/, etc.)
  - Tests (tests/, __tests__/, spec/, etc.)
  - Documentation (docs/, README.md, etc.)
  - Configuration (config/, .config/, etc.)
  - Build/output (dist/, build/, out/, etc.)

### 3. Identify Technologies

Analyze project files to determine:

**Package/Dependency Files:**
- package.json → Node.js/JavaScript/TypeScript
- requirements.txt / pyproject.toml → Python
- go.mod → Go
- Cargo.toml → Rust
- Gemfile → Ruby
- pom.xml / build.gradle → Java

**Framework Indicators:**
- next.config.js → Next.js
- nuxt.config.js → Nuxt.js
- angular.json → Angular
- vite.config.* → Vite
- webpack.config.* → Webpack
- Dockerfile → Containerized

**Extract:**
- Primary language(s)
- Framework(s)
- Major dependencies
- Development tools

### 4. Analyze Repository Characteristics

Determine:
- **Size:** Small (<50 files), Medium (50-500), Large (500+)
- **Type:** Library, Application, Service, Monorepo
- **Complexity:** Simple, Moderate, Complex (based on directory depth and file count)
- **Domain hints:** From folder names, README, package descriptions

### 5. Check for Existing Documentation

Look for:
- README.md (content summary)
- CONTRIBUTING.md
- docs/ folder
- Architecture documentation
- API documentation
- Any existing knowledge/ or core_knowledge/ folder

### 6. Check user-data/ Folder

If {project-root}/user-data/ exists:
- Count files present
- Note file types (for future processing)

---

## State Update

After scan completes, update workflow-state.json:

```json
{
  "workflow_version": "1.0.0",
  "workflow_type": "repository-initialize",
  "timestamps": {
    "started": "{{now}}",
    "last_updated": "{{now}}"
  },
  "current_step": "step_1_scan",
  "completed_steps": [],
  "findings": {
    "repository_analysis": {
      "project_root": "{{project_root}}",
      "repository_type": "{{type}}",
      "primary_language": "{{language}}",
      "framework": "{{framework}}",
      "total_files": {{count}},
      "total_directories": {{count}},
      "size_category": "{{small|medium|large}}",
      "complexity": "{{simple|moderate|complex}}",
      "existing_docs": ["{{list}}"],
      "user_data_files": {{count}}
    }
  },
  "resume_instructions": "Scan complete. Ready to propose structure."
}
```

---

## Present Results

Show user the scan summary:

```
📊 **Repository Scan Complete**

**Location:** {{project_root}}

**Technology Stack:**
- Language: {{primary_language}}
- Framework: {{framework}}
- Key Dependencies: {{top_5_deps}}

**Repository Characteristics:**
- Type: {{repository_type}}
- Size: {{total_files}} files in {{total_directories}} directories
- Complexity: {{complexity}}

**Key Directories Found:**
{{directory_list_with_descriptions}}

**Existing Documentation:**
{{existing_docs_or_none}}

**user-data/ Status:**
{{user_data_count}} files pending (will be processed separately)

---

[C] Continue to structure proposal
```

---

## Success Metrics

✅ Project root confirmed with user
✅ All major technologies identified
✅ Repository type and size characterized
✅ Key directories mapped
✅ Existing documentation noted
✅ workflow-state.json updated
✅ Scan summary presented clearly

---

## Failure Modes

❌ Scanning without confirming project root
❌ Missing major technologies in analysis
❌ Not checking for existing documentation
❌ Proceeding without user acknowledgment
❌ Not updating workflow-state.json

---

## Next Step

After user selects [C] to continue:

1. Mark step_1_scan as completed in workflow-state.json
2. Load and execute `./step-02-propose.md`

**DO NOT proceed until user explicitly continues!**

