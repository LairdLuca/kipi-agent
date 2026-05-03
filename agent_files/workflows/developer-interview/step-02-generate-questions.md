# Step 2: Generate Questions

## MANDATORY EXECUTION RULES (READ FIRST)

- 🎯 ONE question per gap — exactly 10
- 📝 SHORT and SPECIFIC — never "tell me about X"
- 💬 ALWAYS announce both answer methods (chat OR transcript) when presenting
- 🚪 ALWAYS announce the skip affordance — questions are optional
- ⏸️ Do NOT collect answers in this step; that's step-03

---

## Prerequisites

- Step 1 (Analyze Gaps) must be completed
- workflow-state.json must have `findings.gaps_detected` (10 items)
- User must have approved the gap list

---

## Your Task

For each of the 10 approved gaps, formulate ONE focused interview question. Each question targets context that wouldn't be obvious from reading code alone.

---

## Question Quality Standards

A good question:
- ✅ Targets ONE gap; doesn't try to cover multiple things
- ✅ Asks for context, decisions, history, or trade-offs (the kind of knowledge that lives in heads)
- ✅ Is short (1–3 sentences); the developer can answer in a paragraph
- ✅ Names the location it will inform (so the developer knows where their answer lands)

A bad question:
- ❌ "Tell me about the architecture" (too broad)
- ❌ "How does the auth service work?" (read the code)
- ❌ Multi-part questions that bundle 5 sub-questions

---

## Question Templates by Gap Type

| Gap type | Question pattern |
|---|---|
| `placeholder` (architecture.md) | "What's the highest-level mental model for this system that I'd struggle to derive from code alone? Include any pattern (event-driven, hexagonal, etc.) and why it was chosen." |
| `placeholder` (component file) | "What's the most important thing to know about `{{component}}` that isn't obvious from reading its code — its boundaries, its tricky bits, or a decision that constrains it?" |
| `missing_content` (process file) | "Walk me through the `{{process}}` flow end-to-end: what triggers it, who/what is involved at each step, and where does it commonly fail or need manual intervention?" |
| `identity` (ownership) | "For each of these assets — {{list}} — who owns it (by role, not name yet — we'll capture names separately), and who's the secondary/backup?" |
| `structural` (missing folder despite signal) | "I detected `{{signal}}` in code but there's no `{{folder}}/` documentation. What's the shape of that area — major topics worth a dedicated file?" |
| `stale` (last updated 90+ days, code changed) | "What's changed in `{{location}}` recently that the docs don't capture? Especially decisions or trade-offs that wouldn't show up in a code diff." |

Adapt these templates — never use them verbatim. Each project is different.

---

## Execution Sequence

### 1. Generate questions

For each gap (1–10), produce one question following the standards above.

### 2. Save questions

Update workflow-state.json:

```json
{
  "current_step": "step_2_generate_questions",
  "findings": {
    "questions_generated": [
      {"id": 1, "gap_id": 1, "target_location": "architecture.md", "question": "...", "target_role_focus": "..."},
      ...
    ]
  }
}
```

### 3. Present to user

Use this exact framing for the user-facing announcement:

```
📝 **10 Questions to Fill Knowledge Gaps**

I generated one focused question per gap. Each question is targeted at knowledge
that lives in your head and not in the code.

**You can answer in two ways:**
1. **In chat** — reply with answers using a numbered format (`1. ...`, `2. ...`, etc.).
2. **Upload a transcript** — drop a text file (meeting notes, voice-memo transcript,
   recorded interview transcript) into:
       knowledge/private_knowledge/raw_data/meetings_transcripts/
   Then tell me the filename and I'll extract the answers from it.

**You don't have to answer any question that doesn't seem relevant.** Just say
"skip" (or skip it in your transcript). There's no penalty — Kipi will move on.

---

## The Questions

### 1. (→ `{{target_location}}`)
{{question_1}}

### 2. (→ `{{target_location}}`)
{{question_2}}

...

### 10. (→ `{{target_location}}`)
{{question_10}}

---

**When you're ready, reply with your answers (or tell me the transcript filename).**
**Or hit `[E]` to edit a question first.**
```

⏸️ **STOP and WAIT for user input.**

---

## Handle User Response

- User starts answering → advance to step-03 (collect answers)
- User uploads transcript → user gives filename → advance to step-03 with `answer_method = transcript`
- [E] Edit → ask which question, regenerate, re-present full list

---

## Success Metrics

✅ Exactly 10 questions, one per approved gap
✅ Each question is short and specific
✅ Each question names its target location
✅ The two-method affordance was announced verbatim
✅ The skip affordance was announced explicitly
✅ Questions saved in workflow-state.json

---

## Failure Modes

❌ Bundled multi-part questions
❌ Generic "tell me about X" questions
❌ Forgetting to announce chat-OR-transcript option
❌ Forgetting to announce skip affordance
❌ Generating > 10 or < 10 questions

---

## Next Step

When user begins answering (or names transcript file):
1. Update workflow-state.json with `answer_method`
2. Add `step_2_generate_questions` to completed_steps
3. Load and execute `./step-03-collect-answers.md`
