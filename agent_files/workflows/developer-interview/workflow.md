# Developer Interview Workflow

**Workflow ID:** `developer-interview`
**Purpose:** Fill Core-Knowledge gaps via 10 targeted developer interview questions
**Menu Trigger:** `[DI]` or fuzzy match on `developer-interview`, `interview`, `fill gaps`

---

## Overview

Kipi analyzes the current state of `core_knowledge/`, identifies the 10 most valuable gaps to close, generates one question per gap, collects answers from the developer (in chat or via uploaded transcript), distills the answers into name-free, role-based knowledge, and proposes specific updates across `core_knowledge/`.

The user can answer each question in chat or skip it; or they can upload a transcript that covers some/all questions and Kipi will extract the relevant answers.

---

## Configuration

```yaml
workflow_type: developer-interview
state_file: {project-root}/_bmad/_memory/kipi-sidecar/workflow-state.json
core_knowledge_root: {project-root}/knowledge/core_knowledge/
private_knowledge_root: {project-root}/knowledge/private_knowledge/
transcript_inbox: {project-root}/knowledge/private_knowledge/raw_data/meetings_transcripts/
templates:
  questions: {agent-root}/templates/developer-interview/questions-template.md
  answers: {agent-root}/templates/developer-interview/answers-template.md
checklist: {agent-root}/checklists/developer-interview-checklist.md
question_count: 10
```

---

## Workflow Architecture

```
workflow.md (this file)
├── step-01-analyze-gaps.md       # Detect what's missing in core_knowledge
├── step-02-generate-questions.md # Produce 10 targeted questions, present + offer skip
├── step-03-collect-answers.md    # Collect via chat OR transcript; allow skips
└── step-04-integrate.md          # Distill answers into core_knowledge updates with self-check
```

---

## Prerequisites

Before starting:
1. `knowledge/core_knowledge/index.md` must exist (run `[RI]` first)
2. `knowledge/private_knowledge/raw_data/meetings_transcripts/` must exist (created by `[RI]`)
3. Recommended: `[CS] Code Scan Document` has been run at least once

---

## Execution Steps

### Step 1: Analyze Gaps
**File:** `./step-01-analyze-gaps.md`

- Read the routing table, coverage table, and Folder Contracts section from `index.md`
- Walk every baseline folder + approved optional folders
- Score each location for "knowledge gap": placeholder text, low file count, file marked "⬜ Not written", folder absent of any content files, missing `_template.md` despite multiple similar files, narrowed/expanded contract relative to the canonical scope
- Cross-reference against codebase signals from prior scans
- Produce a ranked list of the top 10 gap candidates with rationale
- ⏸️ STOP, present ranked gap list, ask user "approve / re-rank / drop some"

### Step 2: Generate Questions
**File:** `./step-02-generate-questions.md`

- For each of the 10 approved gaps, formulate ONE focused question
- Each question:
  - Names the gap it addresses (so user knows what it's filling)
  - Asks for context that wouldn't be obvious from code alone
  - Is short and specific (not "tell me about the system")
- ⏸️ STOP, present all 10 questions in a numbered list
- Tell user upfront (verbatim):

  > "**You can answer in two ways:**
  > 1. **In chat** — reply with answers. Use formatting like '1. ...', '2. ...', '3. skip', etc.
  > 2. **Upload a transcript** — drop a file (notes, voice-memo transcript, recorded meeting transcript, anything text-based) into `knowledge/private_knowledge/raw_data/meetings_transcripts/`, then tell me the filename and I'll extract answers from it.
  >
  > **You can skip any question that doesn't feel relevant — just say 'skip' or leave it out. There's no penalty.**"

- Save the questions to a working file (in workflow-state.json or a sidecar file)

### Step 3: Collect Answers
**File:** `./step-03-collect-answers.md`

- Wait for user input
- If user replies in chat: parse per-question answers, mark skipped questions
- If user provides a transcript filename: read the file from `private_knowledge/raw_data/meetings_transcripts/`, extract answers per question (matching by topic), mark unanswered as skipped
- For each answer, capture:
  - Original question
  - Raw answer (verbatim)
  - Source: `chat` or `transcript:<filename>`
  - Status: `answered` / `skipped`
- ⏸️ STOP, present a summary table: which questions were answered, which were skipped
- Save raw answers (verbatim) into a sidecar log: `_bmad/_memory/kipi-sidecar/developer-interview/<date>-answers.md`. **Do not write verbatim names or details into `core_knowledge/` yet.**

### Step 4: Integrate Knowledge
**File:** `./step-04-integrate.md`

For each answered question, propose an integration:
- Which file in `core_knowledge/` should be updated/created
- What new content the answer adds (DISTILLED — not verbatim)
- The distillation process applies the Hard Rules:
  - Replace human names with roles
  - Route identity-bearing content into `team_and_stakeholders/`
  - Strip any reference to `private_knowledge/`
- Present each proposal one-at-a-time for user approval
- For each approved proposal, write the file (with self-check before save)
- Update `index.md` coverage table
- Save the run report to `_bmad/_memory/kipi-sidecar/developer-interview/<date>-report.md`

---

## State Management

```json
{
  "workflow_type": "developer-interview",
  "current_step": "step_X_name",
  "completed_steps": [...],
  "findings": {
    "gaps_detected": [
      {"id": 1, "location": "components/auth-service.md", "type": "placeholder-only", "score": 0.95, "rationale": "..."}
    ],
    "questions_generated": [
      {"id": 1, "gap_id": 1, "question": "..."}
    ],
    "answer_method": "chat | transcript",
    "answer_source": "chat | transcript:<filename>",
    "answers_collected": [
      {"q_id": 1, "status": "answered | skipped", "raw_answer_path": "_bmad/_memory/kipi-sidecar/developer-interview/<date>-answers.md#q1"}
    ],
    "integration_proposals": [
      {"q_id": 1, "target_file": "...", "approved": null, "applied": false}
    ]
  },
  "resume_instructions": "..."
}
```

---

## Resumability

Each step is fully resumable:
- Step 1 → can re-run analysis with different ranking
- Step 2 → can edit questions before presenting
- Step 3 → can switch from chat to transcript mid-run
- Step 4 → can approve/skip per integration proposal

---

## Hard Rules (CRITICAL)

These rules govern every file this workflow writes into `core_knowledge/`. Inherited from Kipi persona (`_bmad/kipi/SKILL.md`).

1. **Names of people** appear ONLY in `core_knowledge/team_and_stakeholders/`. When an answer mentions a person, the integration step extracts identity (name, contact, role) into `team_and_stakeholders/contacts.md` or `ownership-map.md`, and uses ROLE in the technical knowledge file.
2. **No `private_knowledge/` references** in `core_knowledge/`. The transcript stays in `private_knowledge/raw_data/meetings_transcripts/`. The digested knowledge is self-contained.
3. **Verbatim answers stay in the sidecar log** (`_bmad/_memory/kipi-sidecar/developer-interview/...`), not in `core_knowledge/`.
4. **Self-check before writing** — every proposed `core_knowledge/` file is scanned for human names outside `team_and_stakeholders/` and any `private_knowledge` paths; redact, reroute, or refuse to write if found.

---

## Completion

When Step 4 completes:
1. Validate against checklist
2. Set workflow state to completed
3. Show completion report:
   - Questions answered / skipped
   - Files updated / created in `core_knowledge/`
   - Identity-bearing content routed into `team_and_stakeholders/`
   - Sidecar log path (where verbatim answers live)
4. Return to Kipi menu

---

## Error Handling

- **Transcript file not found:** Inform user, ask for correct filename or fall back to chat
- **Answer parsing ambiguous:** Ask the user to clarify per-question
- **User skips all 10 questions:** Save state, return to menu, no `core_knowledge/` writes
- **Self-check fails on integration:** Surface the violation, ask user how to handle (rewrite / route / drop)
