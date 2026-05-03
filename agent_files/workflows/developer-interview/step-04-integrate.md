# Step 4: Integrate Knowledge

## MANDATORY EXECUTION RULES (READ FIRST)

- 🧪 DISTILL — never copy verbatim answers into `core_knowledge/`
- 🛡️ ENFORCE Hard Rules on every proposed file (names, no `private_knowledge/` refs, self-check)
- 📋 PROPOSE one-at-a-time, get individual approval per file
- 🔄 UPDATE workflow-state.json after each integration
- ⏸️ STOP after each proposal for user [Y/N/E]
- 📊 Update `index.md` coverage table after all approved integrations

---

## Prerequisites

- Step 3 (Collect Answers) completed
- workflow-state.json has `findings.answers_collected` with at least one `answered` entry
- Sidecar log at `_bmad/_memory/kipi-sidecar/developer-interview/<date>-answers.md` exists

---

## Your Task

For each ANSWERED question, distill the raw answer into a name-free, role-based update for the right file in `core_knowledge/`. Identity-bearing content (names, contacts, ownership) gets routed into `team_and_stakeholders/`. Propose each integration to the user one-at-a-time. After approval, write the file with a Hard-Rules self-check.

---

## Execution Sequence

### 1. For each answered question, build an integration proposal

For Q_id with status `answered`:

**a. Read the verbatim answer** from the sidecar log (don't load all of them; load one at a time and purge after).

**b. Classify the answer's content** into:
- **Technical** (architecture, components, processes, infrastructure, decisions, integrations)
- **Identity** (names, contact info, who-owns-what)
- **Mixed** (both — split into two updates)

**c. For each classification, draft an update:**

**Technical content:**
- Target file: the question's `target_location` (or a derived sub-file under it)
- Distill: rewrite the verbatim answer in the project's documentation voice
- Replace any human names with ROLES
- Strip any `private_knowledge` references
- Apply the relevant template's section structure

**Identity content:**
- Target file: `team_and_stakeholders/contacts.md`, `team-structure.md`, or `ownership-map.md` (whichever fits)
- Add the names + contacts + roles
- Cross-link from the technical file (use a role + link, not a name inline)

### 2. Run the Hard Rules self-check

For every proposed file body, BEFORE presenting to user:

| Check | Pass criteria |
|---|---|
| Name scan | No human first/last names appear OUTSIDE files in `team_and_stakeholders/` |
| `private_knowledge` literal | The string `private_knowledge` does NOT appear in the body |
| `private_knowledge` paths | No path starts with `knowledge/private_knowledge/` |
| Verbatim leakage | The body is a distillation, not a copy of the sidecar verbatim text |

If ANY check fails: do not present the proposal. Re-distill or split the content. If you cannot make it pass, surface the difficulty and ask the user how to handle.

### 3. Present the proposal (one-at-a-time)

```
🧩 **Integration Proposal for Q{{n}} (→ `{{target_file}}`)**

Question: {{question}}
Source: {{chat | transcript:<filename>}}

**What will change:**
- Target: `{{target_file}}` ({{create | append-section | update-section}})
- Section affected: {{section_name}}

**Proposed content (distilled, name-free, role-based):**

```markdown
{{distilled_content}}
```

{{#if identity_content_present}}
**Plus identity content routed to `team_and_stakeholders/`:**
- File: `{{contacts_or_ownership_or_team_structure}}`
- Adds: {{summary_of_identity_additions}}

{{/if}}

**Hard Rules self-check:** ✅ Names check / ✅ private_knowledge check / ✅ no verbatim leakage

[Y] Apply this proposal
[N] Skip this proposal (don't apply)
[E] Edit — what should change?
```

⏸️ **STOP and WAIT for user input.**

### 4. Apply approved proposals

For each [Y]:
1. Re-run the self-check immediately before saving (defense in depth)
2. Write the file (create | append-section | update-section)
3. If identity content was extracted: also write to the relevant `team_and_stakeholders/` file
4. Report each save: "✓ Updated `{{file}}`"
5. Update workflow-state.json:

```json
{
  "findings": {
    "integration_proposals": [
      {"q_id": 1, "target_file": "...", "approved": true, "applied": true, "applied_at": "..."}
    ]
  }
}
```

### 5. Update index.md coverage table

After all approved integrations are applied:
- Update each touched location's `Status`, `Last updated`, `File count` in `index.md` Coverage table
- Save the run report to `_bmad/_memory/kipi-sidecar/developer-interview/{YYYY-MM-DD}-report.md`

### 6. Cleanup

- Mark `current_step = "completed"`
- Add `step_4_integrate` to completed_steps
- 🧹 PURGE detailed integration data from context; keep summary

---

## Run Report (saved to sidecar)

```markdown
# Developer Interview — Run Report

**Date:** {{date}}
**Source:** {{chat | transcript:<filename>}}

## Summary

- Questions answered: {{n}}/10
- Integrations proposed: {{m}}
- Integrations applied: {{k}}
- Files touched in `core_knowledge/`: {{list}}
- Identity routes to `team_and_stakeholders/`: {{count}}

## Hard Rules Compliance

- All proposed files passed name-check: ✅
- All proposed files passed private_knowledge-check: ✅
- All proposed files passed verbatim-leakage-check: ✅
- Identity content routed to `team_and_stakeholders/`: ✅

## Next Suggestions

- {{auto-generated next steps based on remaining gaps}}
```

---

## Completion Report (shown to user)

```
✅ **Developer Interview Complete!**

**Answered:** {{n}}/10 questions
**Integrations applied:** {{k}}/{{m}} proposed

**Files updated in `core_knowledge/`:**
- {{list}}

**Identity content routed to `team_and_stakeholders/`:**
- {{list}}

**Sidecar artifacts (not in core_knowledge):**
- Verbatim answers: `_bmad/_memory/kipi-sidecar/developer-interview/{{date}}-answers.md`
- Run report: `_bmad/_memory/kipi-sidecar/developer-interview/{{date}}-report.md`

**Hard Rules:** all proposals passed self-check ✅

**Next Steps:**
- Review the updated files in `core_knowledge/`
- Run `[ST]` to see the new coverage
- Run `[CL]` if you want a maintenance pass

[Done] Return to Kipi menu
```

---

## Hard Rules (CRITICAL — applied to every file written by this step)

These are the same rules inherited from Kipi persona. Restated here because this step is the highest-risk write step in the workflow:

1. **Names** — every body written into `core_knowledge/` MUST scan clean for human names UNLESS the target file is inside `team_and_stakeholders/`.
2. **`private_knowledge/` references** — every body written into `core_knowledge/` MUST NOT contain the string `private_knowledge` or any path starting with `knowledge/private_knowledge/`.
3. **No verbatim leakage** — content is distilled, not pasted. The verbatim record stays in the sidecar log.
4. **Defense in depth** — the self-check runs (a) before presenting the proposal, (b) immediately before saving the file. Both checks must pass.

If either check fails at save time: do not write the file. Surface the issue, propose a redaction, get user approval, retry.

---

## Success Metrics

✅ Every answered question produced an integration proposal
✅ Every proposal was reviewed by the user
✅ Every applied file passed Hard Rules self-check (twice)
✅ Identity content was routed to `team_and_stakeholders/`, never embedded in technical files
✅ `index.md` coverage table updated
✅ Run report saved

---

## Failure Modes

❌ Copying verbatim answers into `core_knowledge/`
❌ Letting names slip into technical files
❌ Letting `private_knowledge` paths into any `core_knowledge/` file
❌ Writing without per-proposal user approval
❌ Skipping the index coverage update

---

## Workflow Complete

This is the final step of `developer-interview`.

After completion:
- User can run `[CL]` to verify everything is well-organized
- User can re-run `[DI]` later to fill the next round of gaps
- Workflow state preserved for reference
