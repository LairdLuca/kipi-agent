# {{job_name}}

> **Summary:** {{one_line_summary}}

**Status:** {{active|deprecated}}
**Owner role:** {{role}}
**Last updated:** {{date}}

---

## Purpose

Why this job exists. The business or technical outcome it produces.

## Trigger

How it runs: scheduled (cron / interval), event-driven (webhook, queue), manual.

## Inputs

Parameters, files, environment variables, upstream artefacts.

## Steps

What the job actually does, end to end. Link to source files; don't duplicate code here.

1. {{step}}
2. {{step}}

## Outputs

Artefacts produced, where they land, who consumes them.

## Configuration

Where the job's config lives, the knobs that matter, sane defaults.

## Observability

Logs, metrics, dashboards, alerts. Where to look first when it misbehaves.

## Failure modes and recovery

The common ways this job breaks and the recovery action for each.

---

_Copy this file as `kebab-case.md` inside the relevant `infrastructure/` sub-folder (e.g. `infrastructure/jobs/`). Delete sections that don't apply._
