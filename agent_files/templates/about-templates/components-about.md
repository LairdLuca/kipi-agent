# Components — Folder Contract

Catalog of the major modules / services / packages in this repo. Each component is one file describing its responsibilities, boundaries, public interface, dependencies, and component-specific decisions.

## What goes here

- One file per major component, named `kebab-case.md`
- Sub-folders for layered architectures: `components/frontend/`, `components/backend/`
- Per-project sub-folders for monorepos (when "hybrid" layout was chosen at init)

## What does NOT go here

- System-wide diagrams or cross-component patterns → `architecture.md`
- Component owners (names) → `team_and_stakeholders/ownership-map.md`
- Multi-step flows that span components → `processes/`
- API specifications → `api/` (Tier 2, if created)
- DB schemas / queries → `data_models/` (Tier 2, if created)

## Hard rules

- Reference roles, never names. Names live ONLY in `team_and_stakeholders/`.
- Never link to anything inside `private_knowledge/`.

## Naming

`kebab-case.md` (e.g. `auth-service.md`, `payment-processor.md`).
