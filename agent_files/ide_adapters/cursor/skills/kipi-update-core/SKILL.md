---
name: kipi-update-core
description: Companion skill for Kipi. Use after another agent makes code changes in the code repository, before a /clear or the closing of a tab that produced knowledge, or whenever the user asks to update the knowledge vault, sync Kipi, or check what is stale in the vault. Runs Kipi's Update Knowledge workflow without activating the full Kipi persona.
trigger: /kipi-update-core
---

# Kipi — Update Core (stub)

When this skill is invoked (via `/kipi-update-core` or by Kipi selecting [UK]), load and execute the full skill definition from `{{KIPI_HOME}}/kipi-update-core/SKILL.md`. It runs without the Kipi persona: neither the greeting nor the menu is needed.

The file in the Kipi folder is the canonical source. This stub exists for IDE skill discovery.
