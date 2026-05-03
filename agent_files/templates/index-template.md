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
{{#each tier2_folders_present}}
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
{{#each tier2_folders_present}}
| `{{path}}/` | ⬜ Not written | — | 0 |
{{/each}}

---

## Folder contracts

Each folder contains an `_about.md` file describing what belongs there. Read it before adding new content.

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
