# Step 2: Propose Structure

## MANDATORY EXECUTION RULES (READ FIRST)

- 🛑 NEVER implement structure without explicit user approval at this step
- 📋 Present the FULL proposal in ONE review screen — baseline + optional folders + monorepo layout in a single editable form
- 🎯 Pre‑select decisions based on scan signals; let the user toggle anything
- 🔄 UPDATE workflow-state.json after the user approves
- ⚡ If user requests edits, incorporate and re-render the same review screen

---

## Prerequisites

- Step 1 (Scan) must be completed
- workflow-state.json must have `findings.repository_analysis` populated
- User must have acknowledged scan results

---

## Your Task

Present a single review screen that shows the **complete proposed Core-Knowledge structure** for this repo. The user can toggle optional folders, change the monorepo layout, edit folder names, then approve. No multi-gate ceremony — one screen, one approval.

---

## The folder catalog

Internally, every folder Kipi can create has a row in this catalog. Apply the rules to this scan to produce the pre-selected proposal.

| Folder | Stance | Trigger | Pre-select | Default scope |
|---|---|---|---|---|
| `architecture.md` (root file) | mandatory | always | ✅ | system shape, data flow, cross-cutting patterns |
| `components/` | mandatory | always | ✅ | one file per major module / service / package |
| `infrastructure/` | mandatory | always | ✅ | environments, deploy, CI/CD, dev setup, conventions |
| `processes/` | mandatory | always | ✅ | multi-step flows (build, release, onboarding, escalation) |
| `team_and_stakeholders/` | mandatory | always | ✅ | the only place names live (contacts, team-structure, ownership-map) |
| `operations/` | optional | always offered | ⬜ | runbooks, monitoring, alerting, on-call, incident playbooks |
| `integrations/` | optional | external SDK / HTTP client imports | signal-based | external systems this repo talks to (auth, rate limits, failure modes) |
| `api/` | recommended | REST / GraphQL / gRPC endpoints | signal-based | endpoint catalog, request/response, versioning, auth, error contracts |
| `data_models/` | recommended | DB / ORM / schemas detected | signal-based | schemas, ORM models, notable queries, indexes, migration strategy |
| `events/` | recommended | Kafka / RabbitMQ / SQS / EventBridge / NATS | signal-based | topic catalog, schemas, producers, consumers, retention, DLQ |
| `security/` | recommended | auth code / secrets handling / compliance markers | signal-based | authn, authz, secret handling, threat model, compliance posture |
| `ml/` | recommended | ML training / inference code | signal-based | training, inference, evaluation, datasets, features, lifecycle |
| `cli/` | recommended | CLI entry points | signal-based | subcommands, flags, exit codes, configuration, examples |

Mandatory rows are always created. Optional rows are pre-selected based on scan signals; user can toggle.

---

## Execution Sequence

### 1. Compute pre-selections

From `findings.repository_analysis` produced in Step 1, run signal detection (load detectors inline if Step 1 didn't):

- **Monorepo signals** — workspace files (`pnpm-workspace.yaml`, `lerna.json`, `nx.json`, `rush.json`, `turbo.json`), multiple top-level `package.json` / `pyproject.toml`, `packages/` / `apps/` / `services/` / `libs/` directories.
- **API signals** — Express/Fastify/Koa/Hono routes, FastAPI/Flask/Django routes, gRPC `.proto` files, GraphQL schemas, OpenAPI specs.
- **Data model signals** — ORM imports (Prisma, TypeORM, SQLAlchemy, Sequelize, Diesel, GORM), `schema.sql`, `migrations/`, `models/` with class-based ORM patterns.
- **Event signals** — Kafka / RabbitMQ / SQS / EventBridge / NATS / Redis Streams imports.
- **Security signals** — `auth/` folders, JWT / OAuth / Passport imports, secrets-vault clients, compliance markers (SOC2, HIPAA, PCI in docs).
- **ML signals** — torch / tensorflow / sklearn imports, `notebooks/`, `models/` artefacts.
- **CLI signals** — `bin/`, click / typer / commander / cobra imports, executable entry points.
- **Integration signals** — third-party SDK clients (stripe, twilio, sendgrid, slack-sdk, etc.).

For each signal, capture the evidence list (file paths + 1‑line reason).

Store under `findings.scan_signals.<key>`.

### 2. Compute proposed monorepo layout

If monorepo signals fired, default the layout to **hybrid** (recommended). Otherwise `single`.

### 3. Render the review screen

```
📋 **Proposed Core-Knowledge structure**

Project: {{project_name}} ({{repository_type}}, {{primary_language}})

---

**Baseline (always created)**
  ☑ knowledge/core_knowledge/index.md             — routing table + folder contracts (single source of truth)
  ☑ knowledge/core_knowledge/architecture.md
  ☑ knowledge/core_knowledge/components/
  ☑ knowledge/core_knowledge/infrastructure/
  ☑ knowledge/core_knowledge/processes/
  ☑ knowledge/core_knowledge/team_and_stakeholders/
        ├── contacts.md
        ├── team-structure.md
        └── ownership-map.md
  ☑ knowledge/private_knowledge/                  — git-ignored
        ├── raw_data/{emails_team_chats,screenshots,meetings_transcripts}/.gitkeep
        └── personal_tasks/.gitkeep
  ☑ .gitignore (created or appended)              — Kipi block

**Optional folders**  (toggle any line; pre-selected based on what we detected)

  [{{☑|⬜}}] operations/      — always offered
  [{{☑|⬜}}] integrations/    — {{evidence_or_"always offered"}}
  [{{☑|⬜}}] api/             — {{evidence_or_"no signal"}}
  [{{☑|⬜}}] data_models/     — {{evidence_or_"no signal"}}
  [{{☑|⬜}}] events/          — {{evidence_or_"no signal"}}
  [{{☑|⬜}}] security/        — {{evidence_or_"no signal"}}
  [{{☑|⬜}}] ml/              — {{evidence_or_"no signal"}}
  [{{☑|⬜}}] cli/             — {{evidence_or_"no signal"}}

  events/ placement: [{{S|N}}]  S = separate folder    N = nested under data_models/events/

**Monorepo layout**  ({{detected|not detected}})

  ( ) single        — one core_knowledge tree for the whole repo
  ( ) per-project   — each project gets a full core_knowledge subtree
  (●) hybrid        — shared top-level + per-project subtrees under core_knowledge/projects/<name>/  (recommended)

  Detected projects: {{project_list_or_"n/a"}}

---

**Final tree preview**

{{tree_diagram_with_current_selections}}

Total: {{folder_count}} folders, {{file_count}} files

---

**Reply with:**
  [A] Approve — proceed to implementation
  [T] Toggle — e.g. "toggle api off, integrations on, layout single"
  [E] Explain — e.g. "explain events" — show full scope + signals for that folder
  [R] Restart — re-run from scratch
```

⏸️ **STOP and WAIT for user input.**

### 4. Handle response

- **[A]** → save the resolved structure to state, mark step complete, advance to `step-03-implement.md`.
- **[T]** → parse the toggle string, update pre-selections, re-render the review screen.
- **[E]** → show the catalog row for the requested folder (full default scope + signal evidence), then re-render the review screen.
- **[R]** → wipe pre-selections, re-run from Step 1.

---

## State Update

After approval, write to workflow-state.json:

```json
{
  "current_step": "step_2_propose",
  "findings": {
    "scan_signals": {
      "monorepo": {"detected": true|false, "evidence": ["..."], "projects": ["..."]},
      "api": {"detected": true|false, "evidence": ["..."]},
      "data_models": {"detected": true|false, "evidence": ["..."]},
      "events": {"detected": true|false, "evidence": ["..."]},
      "security": {"detected": true|false, "evidence": ["..."]},
      "ml": {"detected": true|false, "evidence": ["..."]},
      "cli": {"detected": true|false, "evidence": ["..."]},
      "integrations": {"detected": true|false, "evidence": ["..."]}
    },
    "structure_proposal": {
      "approved_folders": ["components", "infrastructure", "processes", "team_and_stakeholders", "operations", "api"],
      "events_placement": "separate | nested | not-applicable",
      "monorepo_layout": "single | per-project | hybrid",
      "monorepo_projects": ["..."],
      "user_approved": true,
      "approval_timestamp": "{{now}}"
    }
  },
  "resume_instructions": "Proposal approved; ready to implement."
}
```

---

## Success Metrics

✅ Single review screen rendered with all decisions visible at once
✅ Each optional folder shows its pre-selection rationale (signal evidence or "always offered")
✅ Monorepo layout selectable on the same screen
✅ User approved with a single action ([A])
✅ State captures the full resolved structure

---

## Failure Modes

❌ Rendering separate gates instead of a single review screen
❌ Forcing the user to confirm baseline mandatory items separately
❌ Auto-deciding monorepo layout without showing options
❌ Implementing before approval is recorded in state

---

## Next Step

After [A] Approve:

1. Update workflow-state.json with `user_approved = true` and `approval_timestamp`.
2. Add `step_2_propose` to `completed_steps`.
3. Load and execute `./step-03-implement.md`.

**Do not proceed without explicit approval.**
