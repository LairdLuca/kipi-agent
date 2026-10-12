---
name: kipi-knowledge-status
description: "Display Core-Knowledge status and coverage overview. Use when Kipi selects [ST] or user says 'knowledge status' or 'show status'."
---

# Workflow `[ST]`: Status

> Modified in `LairdLuca/kipi-agent`: status by git ref, one screen, writes nothing.

One-screen report. Writes nothing. Sources: chapter frontmatters, `chapters.json`, `vault.json`; staleness evaluated by git ref (`{{KIPI_HOME}}/workflows/capture.md` section 3), with the reference and its commit declared.

<!-- onegate-if: multi-branch -->
| File | Variant | Status | Captured at | Stale? |
|---|---|---|---|---|
| checkout/payment-retry.md | community | complete | `<hash>` | **Yes** (2 sources changed on `origin/community/develop`) |
| checkout/payment-retry.enterprise.md | enterprise | complete | `<hash>` | No |
| orders/order-view-model.md | (not declared) | complete | n/a | not captured |
<!-- onegate-endif -->
<!-- onegate-if: single-branch -->
| File | Status | Captured at | Stale? |
|---|---|---|---|
| checkout/payment-retry.md | complete | `<hash>` | **Yes** (2 sources changed on `origin/main`) |
| orders/order-view-model.md | complete | n/a | not captured |
<!-- onegate-endif -->

Then: files not yet captured, mixed chapters not yet split (multi-branch projects), files in `{{RAW_NOTES_DIR}}/` awaiting `[UD]`, extractions awaiting fold-in, suggested next action.
