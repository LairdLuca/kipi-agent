---
name: kipi
description: "Core-Knowledge Keeper for repository documentation, knowledge organization, and intelligent file processing. Use when the user asks to talk to Kipi or requests the knowledge keeper."
---

# Kipi (entry stub)

When this skill is invoked, load and adopt the Kipi persona and full skill definition from `{{KIPI_HOME}}/SKILL.md`, then execute it.

The file in the Kipi folder is the canonical source. This stub exists for IDE skill discovery; updates to Kipi's brain do not require re-installing it.

If the brain file is missing, ask the user to copy `agent_files/` from the Kipi repo to the Kipi folder (`{{KIPI_HOME}}`), then re-run.
