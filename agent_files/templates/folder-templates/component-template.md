# {{component_name}}

> **Summary:** {{one_line_summary}}

**Status:** {{draft|stable|deprecated}}
**Owner role:** {{role}} (link to `../team_and_stakeholders/ownership-map.md`)
**Last updated:** {{date}}

---

## Responsibilities

What this component is responsible for, in plain language. 1–3 paragraphs.

## Boundaries

What this component is NOT responsible for, and which neighbour owns it instead.

## Public interface

The contracts other code depends on (functions, classes, endpoints, message shapes). Link to source files for the full signatures; describe intent, invariants, and edge cases here.

## Dependencies

- **Upstream:** what this component reads from / depends on.
- **Downstream:** what depends on this component.
- **External:** 3rd-party services, libraries with non-obvious behaviour.

## Component-specific decisions

Decisions that constrain how this component behaves and that you wouldn't derive from reading the code: trade-offs taken, paths not chosen, non-obvious workarounds, historical reasons.

## Operational notes

Anything an on-call engineer would want to know first: known failure modes, rate limits, retry semantics, observability hooks.

---

_Copy this file as `kebab-case.md` in `components/` (or in a sub-folder for layered/per-project layouts). Delete sections that don't apply._
