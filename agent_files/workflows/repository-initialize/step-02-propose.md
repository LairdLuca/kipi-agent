# Step 2: Propose Structure

## MANDATORY EXECUTION RULES (READ FIRST)

- 🛑 NEVER implement structure without explicit user approval at every gate
- ✅ ALWAYS announce the Tier-1 baseline first; it is mandatory and non-negotiable
- 📋 For each Tier-2 candidate: present ONE-AT-A-TIME with recommendation + rationale + scan evidence; get individual yes/no
- 🌳 If monorepo signals are present: ALWAYS ask the user to pick a layout (a/b/c) — even if signals are strong, never auto-decide
- 🔄 UPDATE workflow-state.json after each gate
- ⚡ If user requests changes, incorporate and re-present that section only

---

## Prerequisites

- Step 1 (Scan) must be completed
- workflow-state.json must have findings.repository_analysis populated, including:
  - `monorepo_signals` (boolean + evidence list)
  - `tier2_signals` (per-folder trigger evidence)
- User must have acknowledged scan results

---

## Your Task

Based on the repository scan, propose the Core-Knowledge structure using the **fixed Tier-1 baseline** plus a curated set of **Tier-2 candidates** based on detected signals. Use a strictly gated, one-by-one approval flow.

---

## Tier-1 Baseline (always created)

These are not negotiable. Announce them and confirm.

**Root files:**
1. `index.md` — routing table + project mission + inline glossary
2. `architecture.md` — system design

**Root folders:**
3. `components/` — modules / services / packages
4. `infrastructure/` — how it runs + how to develop on it
5. `processes/` — multi-step flows that span tools/people/code
6. `team_and_stakeholders/` — contacts, team structure, ownership map (the only place names live)

Plus the mandatory `private_knowledge/` tree under `knowledge/`:
- `private_knowledge/raw_data/emails_team_chats/`
- `private_knowledge/raw_data/screenshots/`
- `private_knowledge/raw_data/meetings_transcripts/`
- `private_knowledge/personal_tasks/`

`.gitkeep` files preserve empty folders. Content of `private_knowledge/` is git-ignored.

---

## Tier-2 Candidates (proposed one-at-a-time)

| Folder | Default stance | Trigger signal |
|---|---|---|
| `operations/` | Always offered | Always (user picks yes/no) |
| `integrations/` | Always offered | External SDK/HTTP client imports detected |
| `api/` | Auto-recommend YES | REST / GraphQL / gRPC endpoints detected |
| `data_models/` | Auto-recommend YES | Database / ORM / schemas detected (catalog includes queries + schemas) |
| `events/` | Auto-recommend YES | Kafka / RabbitMQ / SQS / EventBridge / NATS detected — ASK user if they want it separate or merged into `data_models/` |
| `security/` | Auto-recommend YES | Auth code / secrets handling / compliance markers detected |
| `ml/` | Auto-recommend YES | ML training / inference code detected |
| `cli/` | Auto-recommend YES | CLI entry points detected |

---

## Execution Sequence

### 1. Load Scan Findings

From workflow-state.json, retrieve:
- `repository_type`
- `primary_language`
- `framework`
- `complexity`
- `monorepo_signals` (boolean, evidence list)
- `tier2_signals` (which Tier-2 trigger conditions matched, with evidence)

### 2. Gate A — Confirm Tier-1 Baseline

Present:

```
📁 **Tier-1 Baseline (always created)**

The following will be created in `knowledge/core_knowledge/`:
- `index.md`            (routing table + mission + glossary)
- `architecture.md`     (system design)
- `components/`         (modules / services / packages)
- `infrastructure/`     (deploy, dev setup, CI/CD, testing)
- `processes/`          (multi-step flows: build, release, onboarding, etc.)
- `team_and_stakeholders/` (contacts, team structure, ownership map)

And the private tree (git-ignored) in `knowledge/private_knowledge/`:
- `raw_data/emails_team_chats/`
- `raw_data/screenshots/`
- `raw_data/meetings_transcripts/`
- `personal_tasks/`

[C] Confirm and continue
[Q] I have a question first
```

Wait for confirmation. If [Q], answer the question and re-prompt.

### 3. Gate B — Monorepo Layout (if applicable)

If `monorepo_signals.detected = true`:

Present:

```
🌳 **Monorepo detected**

Evidence: {{evidence_list}}
Detected projects: {{project_list}}

Choose how to organize Core-Knowledge across these projects:

[A] Single core_knowledge — all projects documented under one
    `components/<project>/` tree.
    Best for: tightly-coupled monorepos.

[B] Per-project core_knowledge — each project gets its own full
    Tier-1 structure under `core_knowledge/<project>/`.
    Best for: independent products living in one repo.

[C] Hybrid (RECOMMENDED) — shared top-level for cross-cutting concerns
    (architecture, infrastructure, team_and_stakeholders, processes)
    + per-project sub-trees under `core_knowledge/projects/<name>/`.
    Best for: most monorepos. Keeps team_and_stakeholders truly singular
    (which Hard Rule #1 requires) while giving each project its own space.

Which layout do you want? [A/B/C]
```

Wait for choice. Save under `findings.structure_proposal.monorepo_layout`.

If `monorepo_signals.detected = false`: skip this gate, set `monorepo_layout = "single"`.

### 4. Gate C — Tier-2 Candidates (ONE AT A TIME)

For each Tier-2 candidate that matches the rules below, present individually.

**Always offer** (regardless of signals):
- `operations/`
- `integrations/`

**Offer when signal matched** (use `tier2_signals` from scan):
- `api/` if REST/GraphQL/gRPC detected
- `data_models/` if DB/ORM detected
- `events/` if Kafka/MQ detected
- `security/` if auth/secrets detected
- `ml/` if ML detected
- `cli/` if CLI detected

For each candidate, present:

```
🧩 **Tier-2 candidate: `{{folder_name}}/`**

Recommendation: {{YES | NO}}
Rationale: {{1-2 sentences}}
Scan evidence: {{evidence_list_or_"always-offered"}}

What does this folder hold?
{{1-paragraph scope}}

[Y] Create it
[N] Skip
[E] Explain more
```

Special handling for `events/`:

```
Should `events/` be its own folder, or nested inside `data_models/`?

[S] Separate `events/` folder (default; preserves contract framing)
[N] Nest under `data_models/events/` (less folders)
[K] Skip events entirely
```

Loop through all matched candidates. After each user choice, save to `findings.structure_proposal.tier2_decisions[<folder>]`.

### 5. Gate D — Final Preview

After all candidates are decided, render the full final structure:

```
📋 **Final Proposed Structure**

knowledge/
├── core_knowledge/
│   ├── index.md
│   ├── architecture.md
│   ├── components/
│   │   └── _about.md
│   ├── infrastructure/
│   │   └── _about.md
│   ├── processes/
│   │   └── _about.md
│   ├── team_and_stakeholders/
│   │   ├── _about.md
│   │   ├── contacts.md
│   │   ├── team-structure.md
│   │   └── ownership-map.md
{{#each approved_tier2_folders}}
│   ├── {{name}}/
│   │   └── _about.md
{{/each}}
{{#if monorepo_hybrid}}
│   └── projects/
{{#each monorepo_projects}}
│       └── {{project_name}}/
│           ├── components/
│           ├── infrastructure/
│           └── processes/
{{/each}}
{{/if}}
└── private_knowledge/                     (git-ignored)
    ├── raw_data/
    │   ├── emails_team_chats/.gitkeep
    │   ├── screenshots/.gitkeep
    │   └── meetings_transcripts/.gitkeep
    └── personal_tasks/.gitkeep

Plus `.gitignore` at project root (created or appended) to keep
private_knowledge/ content out of git.

Total folders: {{folder_count}}
Total files (incl. _about.md, placeholders, .gitkeep): {{file_count}}

[A] Approve and proceed to implementation
[E] Edit a specific section (which?)
[R] Reject and start over
```

---

## State Update

After each gate, update workflow-state.json:

```json
{
  "current_step": "step_2_propose",
  "findings": {
    "structure_proposal": {
      "tier1_confirmed": true,
      "monorepo_layout": "single | a | b | c",
      "monorepo_projects": ["..."],
      "tier2_decisions": {
        "operations": "yes | no",
        "integrations": "yes | no",
        "api": "yes | no | not-offered",
        "data_models": "yes | no | not-offered",
        "events": "separate | nested | skipped | not-offered",
        "security": "yes | no | not-offered",
        "ml": "yes | no | not-offered",
        "cli": "yes | no | not-offered"
      },
      "approved_tier2_folders": ["operations", "api", ...],
      "user_approved": false,
      "approval_timestamp": null
    }
  },
  "resume_instructions": "Awaiting final approval at gate D."
}
```

---

## Handle User Response at Gate D

### If [A] Approve:
1. Update workflow-state.json:
   - `structure_proposal.user_approved = true`
   - `structure_proposal.approval_timestamp = now`
2. Mark step_2 as completed
3. Proceed to `./step-03-implement.md`

### If [E] Edit:
1. Ask: "Which gate? [A=Tier-1 baseline, B=monorepo layout, C=a specific Tier-2 folder]"
2. Re-execute that gate only
3. Re-render Gate D preview
4. Loop until approved

### If [R] Reject:
1. Ask: "What approach would work better?"
2. Reset relevant findings
3. Restart from Gate A

---

## Success Metrics

✅ Tier-1 baseline confirmed without negotiation
✅ Monorepo layout explicitly chosen by user (when applicable)
✅ Each Tier-2 candidate decided individually with rationale shown
✅ User saw full structure preview before final approval
✅ workflow-state.json captures all decisions

---

## Failure Modes

❌ Skipping the Tier-1 confirmation gate
❌ Auto-deciding monorepo layout without asking
❌ Bundling Tier-2 candidates into a single approval
❌ Implementing without final approval at Gate D
❌ Not capturing per-Tier-2 decisions in state

---

## Next Step

After user approves at Gate D:

1. Update workflow-state.json with approval
2. Add step_2_propose to completed_steps
3. Load and execute `./step-03-implement.md`

**DO NOT proceed until user explicitly approves at Gate D!**
