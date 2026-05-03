# {{original_filename}}

> **Summary:** {{one_line_summary}}

**Source:** `{{original_path}}`
**Type:** {{content_type}}
**Processed:** {{processing_date}}
**Archived To:** `{{archive_path}}`

---

## Table of Contents

{{#each sections}}
- [{{name}}](#{{slug}})
{{/each}}

---

## Overview

{{overview_paragraph}}

### Key Points

{{#each key_points}}
- {{this}}
{{/each}}

{{#if has_action_items}}
### Action Items

{{#each action_items}}
- [ ] {{description}}{{#if owner}} — _{{owner}}_{{/if}}{{#if due_date}} (Due: {{due_date}}){{/if}}
{{/each}}
{{/if}}

{{#if has_decisions}}
### Decisions Made

{{#each decisions}}
- **{{decision}}**{{#if rationale}} — _{{rationale}}_{{/if}}
{{/each}}
{{/if}}

---

{{#each sections}}

## {{name}}

{{content}}

{{/each}}

---

## Metadata

| Field | Value |
|-------|-------|
| Original Filename | `{{original_filename}}` |
| Content Type | {{content_type}} |
| Processing Date | {{processing_date}} |
| Word Count | {{word_count}} |
| Extracted Topics | {{#each topics}}{{this}}{{#unless @last}}, {{/unless}}{{/each}} |

### Classification Suggestion

**Suggested Core-Knowledge Chapter:** {{suggested_chapter}}
**Confidence:** {{classification_confidence}}%
**Rationale:** {{classification_rationale}}

---

## Original Content Reference

The original file has been archived to: `{{archive_path}}`

{{#if has_attachments}}
### Attachments

{{#each attachments}}
- `{{filename}}` — {{description}}
{{/each}}
{{/if}}

---

_Processed by Kipi — Core-Knowledge Keeper_

