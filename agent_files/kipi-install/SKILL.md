---
name: kipi-install
description: "Install or update Kipi for the IDE(s) configured in BMad. Use when Kipi selects [IN] or user says 'install kipi', 'add IDE', or when Kipi auto-detects a missing or outdated install."
---

# Kipi — Install (IDE adapter)

## Overview

Kipi's logic is IDE-agnostic and lives in `_bmad/kipi/`. What changes per IDE is **where the entry-point stubs and rules block are written**. This skill resolves the IDE(s) from BMad config, installs or refreshes the right adapter bundle for each one, and caches the resolution so Kipi can run silently on subsequent activations.

**Single source of truth for the IDE list:** `_bmad/_config/manifest.yaml` (`ides[]`) and the per-IDE files at `_bmad/_config/ides/<ide>.yaml`. Kipi reads, never writes, BMad's config.

## When this runs

Three triggers:

1. **Auto on Kipi activation** — when no `_bmad/kipi/ide-resolved.yaml` exists yet, run silently and emit a one-line confirmation.
2. **Manual `[IN]` from the menu** — when the user wants to refresh stubs, add a newly-configured IDE, or repair a broken install.
3. **`/kipi-install` slash command** (where the IDE supports them).

## On Activation

### Step 1: Resolve project root and BMad presence

- Confirm `{project-root}/_bmad/` exists. If missing → STOP with the standard "BMad is required" message; do not proceed.
- Confirm `{project-root}/_bmad/kipi/SKILL.md` exists (Kipi's brain has been copied in). If missing → tell the user: "Copy `agent_files/` to `_bmad/kipi/` first, then re-run install."

### Step 2: Detect IDEs from BMad config

Read `_bmad/_config/manifest.yaml`. Extract the top-level `ides:` list.

```yaml
# example
ides:
  - cursor
  - claude-code
```

For each IDE name found, also read `_bmad/_config/ides/<ide>.yaml` (if present) for any IDE-specific configuration BMad has stored.

If `ides:` is empty or the file is missing:
- Show a short menu: `cursor`, `claude-code`, `vscode-copilot`, `windsurf`, `cline-roo`, `other (specify)`.
- Ask the user to pick one or more.
- Save the user's selection to `_bmad/kipi/ide-resolved.yaml` only (do NOT modify BMad's manifest).

### Step 3: Resolve install paths per IDE

Use this catalog. Every supported IDE maps to (a) a skills location, (b) a rules file, (c) an optional slash-commands location.

| IDE name (BMad key) | Skills root (relative to project root) | Rules file | Slash commands |
|---|---|---|---|
| `cursor` | `.cursor/skills/` | `.cursorrules` | native (skill name = `/<skill>`) |
| `claude-code` | `.claude/skills/` | `CLAUDE.md` | `.claude/commands/` |
| `vscode-copilot` | (none — Copilot doesn't read skill folders) | `.github/copilot-instructions.md` | `.github/prompts/` (preview) |
| `windsurf` | (none) | `.windsurfrules` | (none) |
| `cline-roo` | (none) | `AGENTS.md` | (none) |
| `other` | ask user | ask user | ask user |

For each IDE, the install bundle to copy is at `_bmad/kipi/ide_adapters/<ide>/`. The bundle layout:

```
_bmad/kipi/ide_adapters/<ide>/
├── skills/                     # only present for IDEs with a skills concept
│   └── <each kipi sub-skill>/SKILL.md
├── commands/                   # only present for IDEs with slash commands
│   └── <command>.md
└── rules-snippet.md            # the rules block to merge into <rules_file>
```

If the bundle for an IDE doesn't exist (e.g. user picked `other`), fall back to: copy generic stubs from `ide_adapters/_generic/` and append the rules from `_bmad/kipi/rules-template.md`.

### Step 4: Show the install plan and confirm

Render exactly what will happen, per IDE:

```
🛠 **Kipi install plan**

Detected from BMad: {{detected_ides}}
Cached previously: {{previously_installed_ides_or_"none"}}

For each IDE, Kipi will:

[cursor]
  ✓ copy 8 skill stubs to .cursor/skills/kipi*/SKILL.md
  ✓ append rules block to .cursorrules (idempotent — header marker)
  - no slash command files needed (Cursor uses native skill triggers)

[claude-code]
  ✓ copy 8 skill stubs to .claude/skills/kipi*/SKILL.md
  ✓ append rules block to CLAUDE.md (idempotent — header marker)
  ✓ copy 3 slash commands to .claude/commands/  (kipi-update-core, kipi-developer-interview, kipi-cleanup-knowledge)

State will be cached at _bmad/kipi/ide-resolved.yaml.

[A] Apply
[E] Edit (toggle an IDE on/off)
[N] Cancel
```

Wait for `[A]` / `[E]` / `[N]`.

### Step 5: Apply per-IDE

For each IDE the user approved, execute:

1. **Skills:**
   - For each `<sub-skill>/SKILL.md` in `_bmad/kipi/ide_adapters/<ide>/skills/`, ensure the destination directory exists and copy the file.
   - Each stub is a thin pointer that loads `_bmad/kipi/<sub-skill>/SKILL.md`. Stubs intentionally have minimal content so updates to Kipi's brain don't require a re-install.

2. **Rules block:**
   - Read `_bmad/kipi/ide_adapters/<ide>/rules-snippet.md` (or fall back to `_bmad/kipi/rules-template.md` if the IDE has no override).
   - The block is bracketed by markers:
     ```
     # === Kipi: Project Rules (managed) — start ===
     ...
     # === Kipi: Project Rules (managed) — end ===
     ```
   - If the target rules file doesn't exist → create it with the block.
   - If it exists and contains the start marker → replace the block in place (idempotent refresh).
   - If it exists without the marker → append (with a leading blank line) using the markers.

3. **Slash commands:** if `commands/` exists in the bundle, copy each `*.md` to the IDE's slash-commands directory.

After each step, report `✓ <action>` or `⚠ <reason skipped>`.

### Step 6: Cache resolution

Write `_bmad/kipi/ide-resolved.yaml`:

```yaml
schema_version: 1
resolved_at: "{{now}}"
detected_via: "bmad-manifest | user-choice | mixed"
ides:
  cursor:
    skills_root: ".cursor/skills"
    rules_file: ".cursorrules"
    slash_commands_root: null
    last_installed: "{{now}}"
    stubs_installed: ["kipi", "kipi-cleanup-knowledge", "kipi-code-scan-document", "kipi-developer-interview", "kipi-knowledge-status", "kipi-process-user-data", "kipi-repository-initialize", "kipi-update-core", "kipi-install"]
    rules_action: "created | replaced | appended | already-current"
  claude-code:
    skills_root: ".claude/skills"
    rules_file: "CLAUDE.md"
    slash_commands_root: ".claude/commands"
    last_installed: "{{now}}"
    stubs_installed: [...]
    commands_installed: [...]
    rules_action: "created | replaced | appended | already-current"
```

This file is what Kipi reads on every subsequent activation to know it doesn't need to re-install.

### Step 7: Completion report

```
✅ **Kipi installed for {{n}} IDE(s)**

For each:
  cursor       — 8 stubs in .cursor/skills/, rules: {{action}}
  claude-code  — 8 stubs in .claude/skills/, 3 slash commands, rules: {{action}}

Resolution cached at: _bmad/kipi/ide-resolved.yaml

You can now invoke Kipi from any of the installed IDEs.

To add another IDE later:
  - configure it in BMad first (`bmad-help` → setup), OR
  - run [IN] Install and pick the IDE manually
```

Return to the Kipi menu (or to the activation flow that triggered the auto-install).

---

## Auto-install behaviour (called from Kipi `On Activation`)

When invoked silently from Kipi's main activation:

1. If `_bmad/kipi/ide-resolved.yaml` exists AND every IDE in `_bmad/_config/manifest.yaml` is present in the cache AND every cached IDE's `last_installed` is fresher than `_bmad/kipi/SKILL.md`'s mtime:
   - Do nothing. Return immediately.
2. If `_bmad/kipi/ide-resolved.yaml` is missing OR a new IDE appears in BMad's manifest:
   - Run Steps 2–6 silently (skip the `[A]/[E]/[N]` prompt for the auto path; default to `Apply` for newly-detected IDEs only — never modify previously-skipped IDEs without asking).
   - Emit a single line: `✓ Kipi installed for {{ide_list}} ({{n}} stubs, rules: {{action}})`.
3. If `_bmad/kipi/SKILL.md` is fresher than every cached `last_installed` (i.e. Kipi was upgraded):
   - Re-render stubs and refresh the rules block (idempotent — same content if nothing changed).
   - Emit: `✓ Kipi refreshed stubs for {{ide_list}} after upgrade`.

The auto path NEVER:
- Modifies `_bmad/_config/` (BMad's territory).
- Adds an IDE the user hasn't configured in BMad.
- Removes stubs (only the user can uninstall via `[IN] Edit`).

---

## Hard Rules (inherited from Kipi persona)

This skill writes outside the usual `core_knowledge/` write zone — it touches `.cursor/skills/`, `.cursorrules`, `CLAUDE.md`, `.claude/skills/`, `.claude/commands/`, etc. To keep that safe:

1. **Idempotent rule blocks.** Always use the `# === Kipi: Project Rules (managed) — start/end ===` markers. Never append without checking. Never replace user content outside the markers.
2. **Per-file action log.** Every file Kipi writes outside `_bmad/kipi/` and `knowledge/` is recorded under `installs[].outputs_generated` in `ide-resolved.yaml`, so the next `[IN] Edit → Uninstall` can reverse it.
3. **No name leakage.** This skill never writes user names anywhere. The rules-snippet content is generic.
4. **Self-check.** Before writing any file, verify it's inside one of the resolved per-IDE paths. If not, refuse.

---

## Resource Reference

| Resource | Path |
|---|---|
| BMad manifest | `_bmad/_config/manifest.yaml` |
| BMad per-IDE config | `_bmad/_config/ides/<ide>.yaml` |
| IDE adapter bundles | `_bmad/kipi/ide_adapters/<ide>/` |
| Generic adapter (fallback) | `_bmad/kipi/ide_adapters/_generic/` |
| IDE-neutral rules template | `_bmad/kipi/rules-template.md` |
| Resolution cache | `_bmad/kipi/ide-resolved.yaml` |

---

## Failure Modes

❌ Modifying `_bmad/_config/` (this is BMad's data; we only read it)
❌ Overwriting a rules file without using the start/end markers
❌ Skipping the action log (breaks uninstall)
❌ Auto-installing for an IDE the user explicitly skipped before
❌ Hard-coding paths in any sub-skill instead of reading `ide-resolved.yaml`
