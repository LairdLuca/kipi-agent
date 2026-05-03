# Infrastructure — Folder Contract

Everything about how this repo runs and how to develop on it.

## What goes here

- Environments (local, staging, prod) and how they differ
- Deployment pipelines, CD, release mechanics
- Hosting / cloud configuration
- Local dev setup, tooling, IDE configs
- CI/CD workflows
- Testing strategy and conventions
- Coding conventions and linting

## What does NOT go here

- Application logic → `components/`
- System design or patterns → `architecture.md`
- Incident response / runbooks → `operations/` (Tier 2, if created)
- External integrations → `integrations/` (Tier 2, if created)
- Multi-step orchestrations → `processes/`

## Hard rules

- Reference roles, never names. Names live ONLY in `team_and_stakeholders/`.
- Never link to anything inside `private_knowledge/`.
