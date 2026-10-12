---
name: kipi-install
description: "Install or repair Kipi for the editors in use. Use when Kipi's first start shows its install plan, when Kipi selects [IN], when the user says 'install kipi', 'add IDE' or 'repair kipi', or when a workflow proposes a repair."
---

# Kipi — Install (IDE adapter)

> Modified in `LairdLuca/kipi-agent`: it asks before writing, BMad is optional, an existing stub or command is never overwritten, the install line is deleted as the last step, the counts are true.

## Overview

Kipi's logic is editor-agnostic and lives in `{{KIPI_HOME}}`. What changes per editor is **where the entry-point stubs and the rules block are written**. This skill resolves the editors, installs or refreshes the right adapter bundle for each one **after the person says yes**, and records what it wrote so a later refresh, a repair or an uninstall knows it.

Nothing is written before the person answers `[A]`.

<!-- onegate-if: bmad -->
**BMad is installed in this project.** The list of editors is read from BMad's manifest (`_bmad/_config/manifest.yaml`, `ides:`) and from the per-editor files in `_bmad/_config/ides/`. Kipi reads, never writes, BMad's config.
<!-- onegate-endif -->
Without BMad the list of editors is what the person says, and it is written only into the record, `ide-resolved.yaml` in `{{KIPI_HOME}}`, at step 6, after `[A]`.

## When this runs

Three triggers, and no other:

1. **The install line.** At the first activation, when `{{KIPI_HOME}}/SKILL.md` still carries the install line (`KIPI-NOT-INSTALLED:`). Kipi shows the plan of step 4 before its menu and asks. `[N]` writes nothing (no state folder either) and the line stays, so the next activation asks again.
2. **By hand.** `[IN]` from the menu, or the `/kipi-install` command where the editor has commands: to refresh after the brain was copied again, to add an editor, or to repair.
3. **A repair proposed by a workflow.** A stub, a command or the rules block is missing and the person said yes (the rule "Missing files" of `{{KIPI_HOME}}/SKILL.md`).

There is no silent path and no check of the install at activation.

## On Activation

### Step 1: Resolve the workspace and the brain

- Confirm `{{KIPI_HOME}}/SKILL.md` exists (Kipi's brain has been copied in). If missing → tell the user: "Copy `agent_files/` from `LairdLuca/kipi-agent` to `{{KIPI_HOME}}` first, then re-run install." and stop.
- The workspace is `{{WORKSPACE}}`: every target path below is relative to it.

### Step 2: Detect the editors

<!-- onegate-if: bmad -->
Read `_bmad/_config/manifest.yaml`. Extract the top-level `ides:` list.

```yaml
# example
ides:
  - cursor
  - claude-code
```

For each editor found, also read `_bmad/_config/ides/<ide>.yaml` (if present) for any editor-specific configuration BMad has stored.
<!-- onegate-endif -->

If the list is empty or missing (and every install without BMad):
- Show a short menu: `cursor`, `claude-code`, `vscode-copilot`, `windsurf`, `cline-roo`, `other (specify)`.
- Ask the user to pick one or more.
- Keep the selection for the plan of step 4. It is written only into the record at step 6, after `[A]`, and never into BMad's config.

### Step 3: Resolve install paths per editor

Use this catalog. Every supported editor maps to (a) a skills location, (b) a rules file, (c) an optional slash-commands location.

| Editor name | Skills root (relative to the workspace) | Rules file | Slash commands |
|---|---|---|---|
| `cursor` | `.cursor/skills/` | `.cursorrules` | native (skill name = `/<skill>`) |
| `claude-code` | `.claude/skills/` | `CLAUDE.md` | `.claude/commands/` |
| `vscode-copilot` | (none, Copilot does not read skill folders) | `.github/copilot-instructions.md` | `.github/prompts/` (preview) |
| `windsurf` | (none) | `.windsurfrules` | (none) |
| `cline-roo` | (none) | `AGENTS.md` | (none) |
| `other` | ask user | ask user | ask user |

For each editor, the install bundle to copy is at `{{KIPI_HOME}}/ide_adapters/<ide>/`. The bundle layout:

```
{{KIPI_HOME}}/ide_adapters/<ide>/
├── skills/                     # only present for editors with a skills concept
│   └── <each kipi sub-skill>/SKILL.md
├── commands/                   # only present for editors with slash commands
│   └── <command>.md
└── README.md                   # editors without skill folders: only a note
```

The rules block is always `{{KIPI_HOME}}/rules-template.md`: no adapter overrides it. If the bundle for an editor does not exist (for example the person picked `other`), fall back to `{{KIPI_HOME}}/ide_adapters/_generic/README.md` (three questions, then only what is possible) and the same rules template.

### Step 4: Show the install plan and confirm

Render exactly what will happen, per editor, with the true counts. A stub or a command that already exists is never overwritten: it is shown as "exists, kept" (and "differs" when its text is not Kipi's: it is the person's file), and the count to create leaves it out:

```
🛠 **Kipi install plan**

Editors: <ide_list>
Recorded earlier: <previous_ides_or_"none">

For each editor, Kipi will:

[claude-code]
  ✓ copy 8 skill stubs to .claude/skills/ (`kipi` exists already, 7 to create)
  ✓ copy 5 slash commands to .claude/commands/ (kipi-update-core, kipi-update, kipi-developer-interview, kipi-cleanup-knowledge, kipi-install)
  ✓ rules block in CLAUDE.md: <create | replace in place | append> (between its own markers)

[cursor]
  ✓ copy 8 skill stubs to .cursor/skills/
  ✓ rules block in .cursorrules: <create | replace in place | append>
  - no slash command files needed (Cursor uses native skill triggers)

Then: record what was written in `ide-resolved.yaml` in {{KIPI_HOME}}, and delete the install line of {{KIPI_HOME}}/SKILL.md, if it is there.
After [A], Kipi also creates its state folder {{KIPI_STATE_DIR}}/ if it is not there (step 6 of its activation); after [N] it creates none.

Nothing is written before [A].
[A] Apply
[E] Edit (toggle an editor on/off)
[N] Cancel (nothing is written)
```

Wait for `[A]` / `[E]` / `[N]`. `[N]` writes nothing at all and leaves the install line where it is.

### Step 5: Apply per editor

For each editor the user approved, execute:

1. **Skills:**
   - For each `<sub-skill>/SKILL.md` in `{{KIPI_HOME}}/ide_adapters/<ide>/skills/`, ensure the destination directory exists and copy the file only when the destination file does not exist. One that exists is left alone, identical or not, and reported as kept.
   - Each stub is a thin pointer that loads `{{KIPI_HOME}}/<sub-skill>/SKILL.md`. Stubs intentionally have minimal content so updates to Kipi's brain do not require a re-install.

2. **Rules block:**
   - Read `{{KIPI_HOME}}/rules-template.md`.
   - The block is bracketed by markers:
     ```
     # === Kipi: Project Rules (managed) — start ===
     ...
     # === Kipi: Project Rules (managed) — end ===
     ```
   - If the target rules file doesn't exist → create it with the block.
   - If it exists and contains the start marker → replace the block in place (idempotent refresh).
   - If it exists without the marker → append (with a leading blank line) using the markers.

3. **Slash commands:** if `commands/` exists in the bundle, copy each `*.md` to the editor's slash-commands directory only when the destination file does not exist. One that exists is left alone, identical or not, and reported as kept.

After each step, report `✓ <action>`, `= <file> exists, kept` or `⚠ <reason skipped>`. For a kept file that differs, say that it is not Kipi's text and that deleting it and running `[IN]` again installs Kipi's. If a write is stopped (for example by the editor's permission check), say which file and what the person can do, and do not work around it: the install is not complete, so step 8 does not run.

### Step 6: Record what was written

Write `ide-resolved.yaml` in `{{KIPI_HOME}}`:

```yaml
schema_version: 1
resolved_at: "<now>"
detected_via: "bmad-manifest | user-choice | mixed"
ides:
  cursor:
    skills_root: ".cursor/skills"
    rules_file: ".cursorrules"
    slash_commands_root: null
    last_installed: "<now>"
    stubs_installed: ["kipi", "kipi-cleanup-knowledge", "kipi-code-scan-document", "kipi-developer-interview", "kipi-knowledge-status", "kipi-process-user-data", "kipi-repository-initialize", "kipi-update-core"]
    rules_action: "created | replaced | appended | already-current"
  claude-code:
    skills_root: ".claude/skills"
    rules_file: "CLAUDE.md"
    slash_commands_root: ".claude/commands"
    last_installed: "<now>"
    stubs_installed: ["kipi", "kipi-cleanup-knowledge", "kipi-code-scan-document", "kipi-developer-interview", "kipi-knowledge-status", "kipi-process-user-data", "kipi-repository-initialize", "kipi-update-core"]
    commands_installed: ["kipi-cleanup-knowledge", "kipi-developer-interview", "kipi-install", "kipi-update", "kipi-update-core"]
    rules_action: "created | replaced | appended | already-current"
```

List in `stubs_installed` and `commands_installed` only the files this install wrote, and keep the entries of an earlier record for files that still exist. A file that existed before and was kept is not listed: it is not Kipi's, and an uninstall must not touch it.

This file is a **record**: it says what was written, so `[IN]`, a repair and an uninstall know it. Kipi does not read it at activation and does not compare it with anything there.

### Step 7: Completion report

```
✅ **Kipi installed for <n> editor(s)**

For each:
  cursor       : <n> stubs written in .cursor/skills/ (<m> kept), rules: <action>
  claude-code  : <n> stubs written in .claude/skills/ (<m> kept), <n> slash commands written (<m> kept), rules: <action>

Record: ide-resolved.yaml in {{KIPI_HOME}}

You can now invoke Kipi from any of the installed editors.

To add another editor later: run [IN] Install and pick it.
```

<!-- onegate-if: bmad -->
(With BMad, an editor can also be configured in BMad first and then picked here.)
<!-- onegate-endif -->

### Step 8: Delete the install line

This is the **last action**, and it runs only after steps 5 to 7 all succeeded.

- If `{{KIPI_HOME}}/SKILL.md` has a line that begins `KIPI-NOT-INSTALLED:`, delete that one line and change nothing else in the file (no blank line left behind, none joined). If it has no such line (a refresh, a repair, a second editor), do nothing.
- If the write is stopped by the permission check, do not try another way to write the file. Say that the install is done, quote the line, ask the person to delete it by hand, and say that the install plan will be offered again at every start until then.
- If any earlier step failed or was cancelled, the line stays: the next activation offers the install again. This is how an install interrupted halfway is noticed.

Return to the Kipi menu (or to the activation flow that started the install).

---

## Hard Rules (inherited from Kipi persona)

This skill writes outside the usual write zone of the vault. It touches `.cursor/skills/`, `.cursorrules`, `CLAUDE.md`, `.claude/skills/`, `.claude/commands/`, and, inside `{{KIPI_HOME}}`, the record and the install line of `SKILL.md`. To keep that safe:

1. **Ask first.** Nothing is written before the person answers `[A]`.
2. **Idempotent rule blocks.** Always use the start and end marker lines of the block, exactly as in step 5. Never append without checking. Never replace user content outside the markers.
3. **Record of every write.** Every file Kipi writes outside `{{KIPI_HOME}}` and the vault is listed in the record (`stubs_installed`, `commands_installed`, `rules_action`), so an uninstall can reverse it.
4. **No name leakage.** This skill never writes user names anywhere. The rules block is generic.
5. **Self-check.** Before writing any file, verify it is inside one of the resolved per-editor paths, or is the record `ide-resolved.yaml` in `{{KIPI_HOME}}` (step 6) or the install line of `{{KIPI_HOME}}/SKILL.md` (step 8). If not, refuse.
6. **Never modify BMad's config** and never add an editor the person did not choose.

---

## Resource Reference

| Resource | Path |
|---|---|
| Editor adapter bundles | `{{KIPI_HOME}}/ide_adapters/<ide>/` |
| Generic adapter (fallback) | `{{KIPI_HOME}}/ide_adapters/_generic/` |
| Editor-neutral rules template | `{{KIPI_HOME}}/rules-template.md` |
| Record | `ide-resolved.yaml` in `{{KIPI_HOME}}` |

---

## Failure Modes

❌ Writing anything before the person answered `[A]`
❌ Modifying BMad's config (this is BMad's data; we only read it)
❌ Overwriting a rules file without using the start/end markers
❌ Skipping the record (breaks repair and uninstall)
❌ Removing the install line before the install has succeeded
❌ Hard-coding paths in any sub-skill instead of using `{{KIPI_HOME}}`
