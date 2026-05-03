# Developer Interview — Raw Answers

**Date:** {{date}}
**Source:** {{chat | transcript:<filename>}}
**Questions answered:** {{n}}/10
**Project:** {{project_name}}

---

## Notice

This file holds VERBATIM answers, including any names or private context the developer chose to share. It lives in the Kipi sidecar (`_bmad/_memory/kipi-sidecar/developer-interview/`) and is NOT part of `core_knowledge/`.

The DISTILLED, name-free, role-based version of these answers is what lands in `core_knowledge/`, after applying Hard Rules during the integration step.

---

{{#each answers}}
## Q{{id}}. (→ `{{target_location}}`)

**Question:** {{question}}
**Status:** {{answered | skipped}}

{{#if answered}}
**Answer (verbatim):**

> {{verbatim_answer}}

{{else}}
**Answer:** _skipped_

{{/if}}
---

{{/each}}

## Run Metadata

| Field | Value |
|---|---|
| Source | {{chat | transcript:<filename>}} |
| Started | {{started_at}} |
| Completed | {{completed_at}} |
| Total time | {{duration}} |
