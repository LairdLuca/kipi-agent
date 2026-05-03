# Step 1: Analyze Gaps

## MANDATORY EXECUTION RULES (READ FIRST)

- 🔍 ANALYZE before generating questions — questions are only useful if they target real gaps
- 📋 PRODUCE a ranked list, not a flat list
- ⏸️ STOP and let the user adjust the gap list before generating questions
- 🔄 UPDATE workflow-state.json with detected gaps

---

## Prerequisites

- `knowledge/core_knowledge/index.md` exists
- workflow-state.json initialized for `developer-interview`

---

## Your Task

Walk the entire `core_knowledge/` tree, score every location for "knowledge gap," and produce a ranked list of the **top 10 gap candidates** with rationale.

---

## Execution Sequence

### 1. Read the routing & coverage tables

From `knowledge/core_knowledge/index.md`:
- The Routing Table — gives the canonical list of locations
- The Coverage table — gives current status (`⬜ Not written` is a strong gap signal)

### 2. Walk the structure

For each folder/file in `core_knowledge/`:
- Check `_about.md` — is it just the contract or has scope been narrowed/expanded?
- Check files inside — are they placeholder-only, partial, or substantive?
- Cross-reference codebase signals from prior `[CS]` scans (in workflow-state.json from past runs)

### 3. Score each location

Apply this rubric (higher score = bigger gap):

| Signal | Weight |
|---|---|
| File marked `⬜ Not written` in coverage table | +0.40 |
| File contains only placeholder text (`{{...}}` or "to be written") | +0.30 |
| Folder has `_about.md` but no actual content files | +0.25 |
| File last updated > 90 days ago and code in scope has changed | +0.20 |
| Strong codebase signal but missing knowledge file (e.g. Kafka detected, no `events/`) | +0.30 |
| `team_and_stakeholders/` files are empty/placeholder | +0.25 (always relevant for ownership clarity) |
| `architecture.md` is empty/placeholder | +0.35 |
| Multi-step process exists in CI/scripts but not documented in `processes/` | +0.30 |

Add weights for each location. Cap at 1.0.

### 4. Rank and select top 10

Sort by score, keep the top 10. Diversify across folders if there's a tie (don't let all 10 come from `components/`).

For each, capture:
- `id` (1–10)
- `location` (file path or folder)
- `gap_type` (placeholder | missing_content | stale | structural | identity)
- `score` (0–1)
- `rationale` (1–2 sentences)
- `target_role_focus` (whether the question should target architecture, ownership, process, decisions, etc.)

### 5. State update

```json
{
  "current_step": "step_1_analyze_gaps",
  "findings": {
    "gaps_detected": [
      {"id": 1, "location": "...", "gap_type": "...", "score": 0.92, "rationale": "...", "target_role_focus": "..."},
      ...
    ]
  },
  "resume_instructions": "Gap analysis complete. Awaiting user adjustment before question generation."
}
```

### 6. Present to user

```
🔍 **Knowledge Gap Analysis**

Walked `core_knowledge/`. Top 10 gaps to close, ranked by impact:

| # | Location | Gap Type | Score | Why |
|---|---|---|---|---|
| 1 | architecture.md | placeholder | 0.95 | System shape & key patterns are unwritten |
| 2 | processes/release-process.md | missing_content | 0.85 | Multi-step CI flow exists in code but not documented |
| 3 | components/auth-service.md | placeholder | 0.80 | _about.md present but no content |
| 4 | team_and_stakeholders/ownership-map.md | identity | 0.75 | Empty — no asset owners recorded |
| ... |

**Adjust the list?**
[A] Approve all 10 — proceed to question generation
[D] Drop specific gaps (which #s?)
[S] Swap a gap (replace # with what?)
[R] Re-rank (give me a different top 10)
```

⏸️ **STOP and WAIT for user input.**

---

## Handle User Response

- [A] → mark step complete, advance to step-02
- [D] → remove specified items, present remaining list (may pull next-ranked candidate to refill to 10)
- [S] → swap as instructed
- [R] → recompute with adjusted weights based on user's input

---

## Success Metrics

✅ Walked the full `core_knowledge/` tree
✅ Produced a ranked top-10 with rationale
✅ User reviewed and adjusted the list
✅ Final list captured in workflow-state.json

---

## Failure Modes

❌ Generating questions before gap analysis is approved
❌ All 10 gaps from one folder (no diversity)
❌ Vague rationale ("seems incomplete")
❌ Skipping `team_and_stakeholders/` from analysis (ownership is always a valid gap)

---

## Next Step

After user approves the list:
1. Update workflow-state.json with approved `gaps_detected`
2. Add `step_1_analyze_gaps` to completed_steps
3. Load and execute `./step-02-generate-questions.md`
