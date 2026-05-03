# Step 3: Collect Answers

## MANDATORY EXECUTION RULES (READ FIRST)

- 📥 Accept BOTH chat replies AND transcript-file uploads
- 🚪 RESPECT skips — never pressure the user; "skip" or omission means move on
- 🔒 STORE verbatim answers ONLY in the sidecar log under `_bmad/_memory/`
- 🚫 NEVER write verbatim answers (or names extracted from them) into `core_knowledge/` in this step — that's step-04 with distillation + self-check
- ⏸️ STOP after collection to show the user a summary

---

## Prerequisites

- Step 2 (Generate Questions) completed
- workflow-state.json has `findings.questions_generated` (10 items)
- User has either started replying with answers OR named a transcript file

---

## Your Task

Map each user-provided answer (or extracted-from-transcript answer) to the corresponding question. Mark unanswered questions as `skipped`. Persist verbatim raw answers in a private sidecar log.

---

## Execution Sequence

### 1. Determine answer source

Check `workflow-state.json` and the latest user message:

- If user is replying inline with a numbered list (`1. ...`, `2. ...`, `3. skip`) → `answer_method = chat`
- If user named a transcript filename → `answer_method = transcript`, `answer_source = transcript:<filename>`

### 2. Read & parse

**Chat path:**
- Parse the user's reply, mapping each numbered item to question id
- Treat any item that's missing, blank, or contains "skip" / "n/a" / "skip me" as `skipped`

**Transcript path:**
- Read the file from `knowledge/private_knowledge/raw_data/meetings_transcripts/<filename>`
- For each question, search the transcript for relevant content using:
  - Keyword matching against the question's target location and key nouns
  - Topic-shape matching (questions about ownership look for names+roles; about processes look for sequence/steps)
- For each question:
  - If a confident match found → extract the relevant span
  - If ambiguous → flag for clarification
  - If nothing relevant → mark `skipped`

### 3. Write the sidecar answer log

Save to `_bmad/_memory/kipi-sidecar/developer-interview/{YYYY-MM-DD}-answers.md`:

```markdown
# Developer Interview — Raw Answers

**Date:** {{date}}
**Source:** {{chat | transcript:<filename>}}
**Questions answered:** {{n}}/10

---

## Q1. (→ `{{target_location}}`)
**Question:** {{question}}
**Status:** {{answered | skipped}}
**Answer (verbatim):**

> {{verbatim_answer_or_"_skipped_"}}

---

## Q2. ...

...

---

_This file lives in `_bmad/` (not committed to user-facing knowledge).
The DISTILLED, name-free, role-based version of these answers will be
proposed for `core_knowledge/` in step-04._
```

This file is the SOURCE OF TRUTH for the verbatim record. It is NOT in `core_knowledge/` and NOT in `private_knowledge/` — it lives in the Kipi sidecar memory area, where Kipi's own working state is kept.

### 4. State update

```json
{
  "current_step": "step_3_collect_answers",
  "findings": {
    "answer_method": "chat | transcript",
    "answer_source": "chat | transcript:<filename>",
    "answers_collected": [
      {"q_id": 1, "status": "answered", "raw_answer_path": "_bmad/_memory/kipi-sidecar/developer-interview/<date>-answers.md#q1"},
      {"q_id": 2, "status": "skipped", "raw_answer_path": null},
      ...
    ]
  },
  "resume_instructions": "Answers collected. Awaiting integration approval."
}
```

### 5. Show collection summary

```
📥 **Answer Collection Complete**

Source: {{chat | transcript:<filename>}}

| # | Target | Status |
|---|---|---|
| 1 | architecture.md | ✅ answered |
| 2 | components/auth-service.md | ✅ answered |
| 3 | processes/release-process.md | ⊘ skipped |
| 4 | team_and_stakeholders/ownership-map.md | ✅ answered |
| ... |

**Answered:** {{n}}/10
**Skipped:** {{m}}/10

Verbatim answers saved to a private sidecar log (not in `core_knowledge/`):
`_bmad/_memory/kipi-sidecar/developer-interview/{{date}}-answers.md`

---

**Ready to integrate?**
[I] Integrate — propose distilled updates for each answered question (one-by-one)
[A] Add more — answer skipped questions, or upload another transcript
[R] Re-collect — discard these answers and start fresh
```

⏸️ **STOP and WAIT for user input.**

---

## Handle User Response

- [I] → advance to step-04
- [A] → loop back: ask which questions to revisit, parse new input, append to sidecar log
- [R] → wipe `answers_collected`, return to start of step-03

---

## Hard Rules (CRITICAL)

This step does NOT write to `core_knowledge/`. It writes only to:
- `_bmad/_memory/kipi-sidecar/developer-interview/<date>-answers.md` (sidecar log; not in core_knowledge, not in private_knowledge)

The verbatim answers may contain names, contact info, and references to people. That's fine in the sidecar — but those will be DISTILLED in step-04 before any `core_knowledge/` write.

If a transcript file is the source, the file itself stays in `private_knowledge/raw_data/meetings_transcripts/`. We never copy its raw content into `core_knowledge/`.

---

## Success Metrics

✅ Every question is mapped to either an answered or skipped state
✅ Skipped questions are honored with no friction
✅ Verbatim answers persist only in the sidecar log
✅ Summary shown to user before integration

---

## Failure Modes

❌ Pressuring the user to answer skipped questions
❌ Writing verbatim answers into `core_knowledge/`
❌ Losing track of which answer maps to which question
❌ Reading a transcript from somewhere outside `private_knowledge/raw_data/meetings_transcripts/`

---

## Next Step

When user chooses [I]:
1. Update workflow-state.json with collection complete
2. Add `step_3_collect_answers` to completed_steps
3. Load and execute `./step-04-integrate.md`
