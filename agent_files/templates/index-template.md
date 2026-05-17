# Core-Knowledge Index — {{project_name}}

> **Repository Type:** {{repository_type}}
> **Primary Language:** {{primary_language}}
> **Last Updated:** {{date}}

---

## Mission

{{project_mission_1_paragraph}}

---

## Routing Table

> Fetch this table first. It tells you whether the answer lives here, somewhere else in `core_knowledge/`, or only in code.

| Location | Has answers about | Does NOT have | Read code instead for |
|---|---|---|---|
| `architecture.md` | system shape, data flow, patterns, cross-cutting concerns | line-level logic | actual class/function bodies |
| `components/` | each module's responsibilities, boundaries, public interface | exact algorithms | unit-test specifics |
| `infrastructure/` | deploy, env, dev setup, CI/CD, testing, conventions | application logic | source files |
| `processes/` | multi-step flows (build, release, onboarding, escalation) | who-owns-what | runtime config |
| `team_and_stakeholders/` | contacts, team structure, ownership map | technical details | n/a |
{{#each optional_folders_present}}
| `{{path}}/` | {{has_answers_about}} | {{does_not_have}} | {{read_code_for}} |
{{/each}}

---

## Glossary

| Term | Definition |
|---|---|
| _(populated as the project develops)_ | |

---

## Coverage

| Area | Status | Last updated | File count |
|---|---|---|---|
| `architecture.md` | ⬜ Not written | — | — |
| `components/` | ⬜ Not written | — | 0 |
| `infrastructure/` | ⬜ Not written | — | 0 |
| `processes/` | ⬜ Not written | — | 0 |
| `team_and_stakeholders/` | ⬜ Not written | — | 0 |
{{#each optional_folders_present}}
| `{{path}}/` | ⬜ Not written | — | 0 |
{{/each}}

---

## Folder Contracts

> Single source of truth for what belongs in each folder. Read the relevant row before creating a new file.

### `architecture.md`
**Holds:** system shape, data flow, cross-cutting patterns, key trade-offs.
**Does NOT hold:** component-level details (use `components/`), runbooks (use `operations/` if present), line-level logic.

### `components/`
**Holds:** one file per major module / service / package, each describing responsibilities, boundaries, public interface, dependencies, component-specific decisions.
**Naming:** `kebab-case.md` (e.g. `auth-service.md`).
**Sub-folders:** allowed for layered architectures (`components/frontend/`) and per-project trees in monorepos.
**Does NOT hold:** system-wide diagrams (→ `architecture.md`), owner names (→ `team_and_stakeholders/ownership-map.md`), multi-step flows (→ `processes/`), API specs (→ `api/` if present), DB schemas (→ `data_models/` if present).

### `infrastructure/`
**Holds:** environments, deployment pipelines, hosting/cloud configuration, local dev setup, tooling, CI/CD, testing strategy, coding conventions and linting.
**Does NOT hold:** application logic (→ `components/`), system design (→ `architecture.md`), incident runbooks (→ `operations/` if present), external integrations (→ `integrations/` if present), multi-step orchestrations (→ `processes/`).

### `processes/`
**Holds:** multi-step flows that span tools, people, and/or code — build, release, onboarding, support/escalation, code review, migrations.
**Naming:** `kebab-case.md` (e.g. `release-process.md`).
**Does NOT hold:** single-step automations in code (→ `infrastructure/`), incident runbooks (→ `operations/` if present), process-owner names (→ `team_and_stakeholders/ownership-map.md`).

### `team_and_stakeholders/`
**Holds:** THE only place in `core_knowledge/` where people's names appear. Required files: `contacts.md`, `team-structure.md`, `ownership-map.md`.
**Does NOT hold:** personal notes (→ `private_knowledge/`), technical decisions (→ relevant component file or `architecture.md`).
**Hard rule:** if you need to refer to a person elsewhere, use a role and link here.

{{#if optional_folders_present}}
### Optional folders enabled in this repo

{{#each optional_folders_present}}
#### `{{path}}/`
**Holds:** {{scope}}
{{#if does_not_have}}**Does NOT hold:** {{does_not_have}}{{/if}}
{{/each}}
{{/if}}

### Universal hard rules

These apply to every folder in `core_knowledge/`:

1. Reference roles, never names. Names live ONLY in `team_and_stakeholders/`.
2. Never link to, cite, embed, or path-mention anything inside `private_knowledge/`.

---

## How to extend this knowledge base

- New raw inputs → drop into `knowledge/private_knowledge/raw_data/`
- Run Kipi: `[UD] Process User Data` to digest into `core_knowledge/`
- Run Kipi: `[UK] Update Core` after code changes
- Run Kipi: `[DI] Developer Interview` to fill knowledge gaps via 10 targeted questions
- Run Kipi: `[CL] Cleanup Knowledge` for periodic maintenance and structure-drift audit

---

## Hard rules enforced by Kipi

1. **Names of people** appear ONLY in `team_and_stakeholders/`. All other Core-Knowledge files reference roles.
2. **`private_knowledge/`** is NEVER referenced (linked, cited, or path-mentioned) from any file in `core_knowledge/`.
3. **`private_knowledge/`** content is NEVER committed to git. Only `.gitkeep` files preserve the structure.

---

_Maintained by Kipi — Core-Knowledge Keeper. Routing-first design._
_Structure initialized: {{initialization_date}}_
