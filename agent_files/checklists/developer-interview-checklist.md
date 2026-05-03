# Developer Interview Workflow — Validation Checklist

**Workflow:** `developer-interview`
**Purpose:** Ensure the 10-question interview was conducted correctly, answers were distilled with Hard Rules enforced, and Core-Knowledge was updated cleanly.

---

## Pre-Interview Checks

- [ ] `knowledge/core_knowledge/index.md` exists
- [ ] `knowledge/private_knowledge/raw_data/meetings_transcripts/` exists
- [ ] Sidecar memory area accessible (`_bmad/_memory/kipi-sidecar/`)

---

## Step 1: Analyze Gaps

- [ ] Walked the full `core_knowledge/` tree
- [ ] Scored every location using the rubric
- [ ] Produced a ranked top-10 list with rationale
- [ ] Diversified across folders (not all 10 from one area unless justified)
- [ ] User reviewed and explicitly adjusted/approved the list
- [ ] State updated: `findings.gaps_detected` populated with 10 items

---

## Step 2: Generate Questions

- [ ] Exactly 10 questions generated, one per gap
- [ ] Each question is short (1–3 sentences) and specific
- [ ] Each question names its target location
- [ ] No multi-part bundled questions
- [ ] Two-method affordance announced (chat OR transcript) — verbatim
- [ ] Skip affordance announced explicitly
- [ ] State updated: `findings.questions_generated` populated

---

## Step 3: Collect Answers

- [ ] Answer source recorded (chat | transcript:<filename>)
- [ ] If transcript: file lives under `private_knowledge/raw_data/meetings_transcripts/`
- [ ] Each question mapped to `answered` or `skipped`
- [ ] Skipped questions honored without friction
- [ ] Verbatim answers persisted ONLY in sidecar log
- [ ] Sidecar log path: `_bmad/_memory/kipi-sidecar/developer-interview/<date>-answers.md`
- [ ] No verbatim content written to `core_knowledge/` in this step
- [ ] User saw collection summary before integration

---

## Step 4: Integrate Knowledge

### Distillation Quality
- [ ] Every applied file is a DISTILLATION, not a copy of verbatim text
- [ ] Identity-bearing content was routed to `team_and_stakeholders/`
- [ ] Technical content uses ROLES, not names

### Hard Rules Self-Check (must pass for every applied file)
- [ ] Name scan: no human names outside `team_and_stakeholders/` ✅
- [ ] `private_knowledge` literal: not present ✅
- [ ] `private_knowledge` paths: not present ✅
- [ ] Self-check ran twice per file (before propose, before save) ✅

### User Approval
- [ ] Each integration was proposed individually
- [ ] User explicitly approved [Y] each applied file
- [ ] Skipped [N] integrations were respected

### Outputs
- [ ] All approved files written to `core_knowledge/`
- [ ] `index.md` coverage table updated for touched locations
- [ ] Run report saved: `_bmad/_memory/kipi-sidecar/developer-interview/<date>-report.md`

---

## Post-Interview Validation

### Forbidden Outcomes (must NOT have happened)
- [ ] ❌ No verbatim answer pasted into `core_knowledge/`
- [ ] ❌ No human names in `core_knowledge/` outside `team_and_stakeholders/`
- [ ] ❌ No string `private_knowledge` in any `core_knowledge/` file written by this run
- [ ] ❌ No path starting with `knowledge/private_knowledge/` in any `core_knowledge/` file written by this run

### Sidecar Hygiene
- [ ] Sidecar answer log exists and contains all answered questions
- [ ] Sidecar run report exists with compliance summary

---

## Common Issues to Check

### Critical Issues (Must Fix)
- [ ] ❌ Verbatim leakage — any sign that step-04 copied raw answer text into `core_knowledge/`
- [ ] ❌ Hard Rules bypass — file written without self-check passing
- [ ] ❌ Skipped questions treated as failures — user pressured to answer

### Quality Issues (Should Fix)
- [ ] ⚠️ Distilled content reads naturally in the documentation voice
- [ ] ⚠️ Cross-links from technical files to `team_and_stakeholders/` use roles + links, not names
- [ ] ⚠️ Coverage table updates accurately reflect what changed

---

## Completion Criteria

All of the following must be true:
1. ✅ 10 gaps analyzed and approved
2. ✅ 10 questions generated and presented
3. ✅ Answers collected (chat or transcript) with skip respected
4. ✅ Verbatim record only in sidecar log
5. ✅ Each integration proposed individually and approved
6. ✅ Every applied file passed Hard Rules self-check (twice)
7. ✅ `index.md` coverage table updated
8. ✅ Run report saved

---

## Sign-Off

- [ ] All checklist items verified
- [ ] User confirmed interview complete
- [ ] No Hard Rules violations in any new `core_knowledge/` content
