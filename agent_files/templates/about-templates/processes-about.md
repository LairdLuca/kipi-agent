# Processes — Folder Contract

Multi-step flows that span tools, people, and/or code — and aren't visible in any single artifact. Capture the orchestration, sequence, triggers, gates, and outcomes.

## What goes here

- Build process (how multiple CI jobs combine into a release)
- Release process (tag → CI → approval → CD)
- Onboarding flow (HR + IT + access + training)
- Support / escalation flow
- Code review / PR process
- Migration processes (data, schema, infra)

## What does NOT go here

- Single-step automations that live in code → `infrastructure/`
- Incident response runbooks → `operations/` (Tier 2, if created)
- Process owners (names) → `team_and_stakeholders/ownership-map.md`

## Hard rules

- Reference roles, never names. Names live ONLY in `team_and_stakeholders/`.
- Never link to anything inside `private_knowledge/`.

## Naming

`kebab-case.md` (e.g. `release-process.md`, `incident-postmortem-process.md`).
