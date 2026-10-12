# Kipi — Core-Knowledge Keeper

> Modified in `LairdLuca/kipi-agent`: a section about this fork at the top, and the sections below corrected for what the fork does.

Kipi manages repository documentation, systematic knowledge organization, and intelligent file processing. It scans your codebase, builds deep documentation organized into chapters, processes the notes you drop into a folder, and keeps everything in sync as your code evolves. Upstream it is a BMad module; on this fork BMad is optional.

---

## About this fork

This fork is upstream's Kipi with the author's modifications inside it, on `main`: the same `agent_files/` brain, the same folders, the same capability codes and the same installer, with a different vault contract, a different way to keep the vault current, and an install that asks first. It is used by [onegate](https://github.com/LairdLuca/onegate), a working configuration for Claude Code that installs it into a project after a short interview, and it can also be installed by hand (below). Upstream's own Kipi, unmodified, is [`ladymeyy/kipi-agent`](https://github.com/ladymeyy/kipi-agent).

**Version 1.1.1** of this fork (tagged `onegate-v1.1.1`), see the [changelog](CHANGELOG.md).

### How it differs from upstream's `main`

| | upstream `main` | this fork's `main` |
|---|---|---|
| Requires BMad | yes, it refuses to activate without it | no: BMad is optional and its config is read only when it is installed |
| Shape | `agent_files/` copied into `_bmad/kipi/`, with per-editor stubs | the same layout, copied into one Kipi folder of your choice (`_bmad/kipi/` when you use BMad) |
| Install | the first activation installs the stubs and the rules block silently | the first activation shows what it will write and asks first; a line of the main skill that says "not installed yet" is deleted after a successful install |
| Initialize (`RI`) | scans the repository and proposes a folder structure | copies a two-file vault skeleton into an empty vault |
| Menu | RI, CS, UD, UK, DI, CL, ST, IN | the same codes; CD and PD also work as aliases of CS and UD |
| Paths | fixed (`knowledge/`, `user-data/`, `_bmad/_memory/`) | placeholders: workspace, code repository, vault, raw-notes inbox, state folder, Kipi folder |
| Vault layout | `knowledge/core_knowledge/` and `private_knowledge/`, folder contracts | `index.md` and `_meta/conventions.md` as the contract; the sections are the project's own |
| Where names live | only in `team_and_stakeholders/` | by role label everywhere; names only in `_meta/roles-and-sources.md`, when the vault has one |
| Code on several branch families | not covered | per-variant capture: every file is captured at one commit on one branch, with a reference branch per variant; a single-branch path exists |
| Staleness | timestamps of files | by git ref, on two references, independent of the checked-out branch |
| Update (`UK`) | drafts a proposal and waits for approval before writing | applies, then reports: the review is the git diff, which stays uncommitted |
| Several keepers at once | not covered | a message protocol, no waiting and no lock (`kipi-update-core`) |
| State | one sidecar workflow-state file | a state folder: per-branch state, chapters, ledger, fold-ins, todo, vault values, notes; created at the first start, after the install's yes |

### Install by hand, without onegate

1. Copy everything under `agent_files/` into the folder you chose for Kipi, `{{KIPI_HOME}}`: `<workspace>/_bmad/kipi` if you use BMad, otherwise `<workspace>/.kipi/agent`.
2. Replace the placeholders below in every copied file, and keep or remove each conditional block.
3. Check: no `{{` and no `onegate-if` or `onegate-endif` is left in the copied files.
4. Copy the entry stub of your editor from the filled Kipi folder, not from the clone (the clone's stub still holds `{{KIPI_HOME}}`): `{{KIPI_HOME}}/ide_adapters/claude-code/skills/kipi/SKILL.md` to `<workspace>/.claude/skills/kipi/SKILL.md` (Claude Code), or `{{KIPI_HOME}}/ide_adapters/cursor/skills/kipi/SKILL.md` to `<workspace>/.cursor/skills/kipi/SKILL.md` (Cursor). Other editors have no skill folder: ask the agent to read `{{KIPI_HOME}}/SKILL.md`.
5. In your editor, started in the workspace, say "talk to Kipi" or run `/kipi`. The first start is described under "First start" below.

**Placeholders** (written with double braces in the files):

| Placeholder | Meaning | Default for use by hand |
|---|---|---|
| `{{PROJECT_NAME}}` | Project name | name of the code repository folder |
| `{{WORKSPACE}}` | Absolute path of the workspace, forward slashes | the folder where you start your editor |
| `{{CODE_REPO}}` | Absolute path of the code repository | the git repository in or below the workspace |
| `{{KIPI_HOME}}` | The folder where `agent_files/` is copied: the brain, its workflows, templates, data and adapters | `<workspace>/_bmad/kipi` with BMad, otherwise `<workspace>/.kipi/agent` |
| `{{VAULT_DIR}}` | The vault | `{{WORKSPACE}}/knowledge` |
| `{{RAW_NOTES_DIR}}` | The raw-notes inbox, with `ready-for-classify/` and `_archive/` inside | `{{WORKSPACE}}/notes` |
| `{{KIPI_STATE_DIR}}` | Kipi's state folder | `{{WORKSPACE}}/.kipi/state` |
| `{{ONEGATE_DIR}}` | Where `onegate-answers.md` would be, if onegate is used | `{{WORKSPACE}}/.onegate` (the file may not exist) |
| `{{ARTIFACTS_DIR}}` | Where test round reports go | `{{WORKSPACE}}/artifacts` |
| `{{CONVERSATION_LANGUAGE}}`, `{{DOCUMENT_LANGUAGE}}` | Languages of the conversation and of the vault | English, English |
| `{{DATA_APPROVER}}` | Role that approves writes to shared data | the human who runs the session |
| `{{BASELINE_VARIANT}}`, `{{VARIANT_RULE}}`, `{{VARIANT_REFERENCE}}` | Baseline variant; how a branch name maps to a variant; the reference branch of a variant | only with several branch families; for example the prefix before the first `/`, and `origin/<variant>/develop` |

**Conditional blocks.** A part of a file that holds only for some answers is wrapped between two marker lines. In markdown: `<!-- onegate-if: <condition> -->` and `<!-- onegate-endif -->`. Keep the content and delete the two marker lines when the condition holds; delete the whole block when it does not. Blocks do not nest. The conditions used in this folder are `multi-branch` (the same code is kept in several long-lived branch families, one per client or edition), `single-branch` (the opposite) and `bmad` (BMad is installed in the project). A project with one branch family keeps the `single-branch` blocks and has no variant files.

The `multi-branch` answer also needs `{{VAULT_DIR}}/_meta/variants.md`, a table of the known variants with their slug and reference branch (onegate ships a template of it); without it Kipi asks. `[UD]` may also name `_meta/roles-and-sources.md` and `_meta/env-troubleshooting-log.md` as the target of an extraction; onegate ships templates of both, and by hand they exist only if you create them or approve their creation when `[UD]` shows its classification.

### First start

The first `/kipi` finds the install line at the top of `{{KIPI_HOME}}/SKILL.md` (a line that begins `KIPI-NOT-INSTALLED:`). Before its menu, Kipi shows a plan and asks: which editors, which stubs and commands it will create, what it will do to the rules file. `[A]` applies, `[E]` edits the list of editors, `[N]` cancels and writes nothing, not even Kipi's state folder, which is created after the yes. After a successful install it deletes that line as its last action, so later starts go straight to the menu with no check of the install. If your editor's permission check stops the deletion, Kipi says so and quotes the line: delete it by hand, or the plan is offered again at every start.

If a workflow later finds a folder or file it needs missing, it says which one, proposes a repair and asks before writing. `[IN]` (or `/kipi-install`) refreshes the install or adds an editor. Copying `agent_files/` again brings the install line back, so the next start asks again.

### Files changed

Each file whose content changed carries one line after its title: "Modified in `LairdLuca/kipi-agent`: ...". These carry none, so they are listed here:

- the 16 editor skill stubs, the 4 editor commands and the 4 editor READMEs under `agent_files/ide_adapters/` (the path of the Kipi folder is the only change, plus one sentence in the READMEs and, for the update stub and command, no persona);
- `workshop/2-INSTALL-PROMPT.txt` and `workshop/3-AGENT-BUILDER-PROMPT.txt`, which are pasted whole into a chat, and `docs/folder-structure.excalidraw`, which is JSON;
- the files this fork adds: `agent_files/workflows/capture.md`, `agent_files/templates/conventions-template.md`, `agent_files/ide_adapters/claude-code/commands/kipi-update.md` (the alias `/kipi-update` of `/kipi-update-core`, Claude Code only) and `CHANGELOG.md` (the versions of this fork).

The 29 files of upstream's folder-contract vault (the step files of the four workflows, the checklists, the batching config, the state schema and twelve templates) are removed: their work is done by the moved workflows. `LICENSE` has one holder line added, and the seven `bmad-skill-manifest.yaml` files are unchanged.

### Credits and license

Kipi, its name, its capability menu, its folder layout and its installer come from [`ladymeyy/kipi-agent`](https://github.com/ladymeyy/kipi-agent) (MIT). The upstream author is credited here with thanks. The license is MIT: the upstream notice is kept unchanged in `LICENSE`, with one holder line added for the additions of this fork.

Where the files say "the original keeper", they mean the private keeper this fork was generalized from, not upstream.

---

## Workshop

For a guided install of this fork (Node.js, Kipi, and BMad if you want it) and a custom-agent building exercise, start at **[START_HERE.md](START_HERE.md)** and use the files in `workshop/`. The install prompt clones `main` of this repository.

---

## Prerequisites

- A code repository under git
- A supported editor (Cursor and Claude Code have skill stubs; Windsurf, VS Code Copilot and Cline or Roo use the rules block only)
- BMad is optional. If you use it, install its latest version first; Kipi then reads which editors you configured and can be registered with it

---

## Installation

### Step 1: Copy agent files (brain)

Copy everything under `agent_files/` into the Kipi folder. The commands, the placeholders and the stub of your editor are in "Install by hand" above.

```bash
mkdir -p <your-project>/.kipi/agent
cp -R agent_files/. <your-project>/.kipi/agent/
```

This includes the main `SKILL.md`, sub-skills (`kipi-*`), workflows, templates, `ide_adapters/` (per-editor install bundles), and `rules-template.md`.

### Step 2: Activate Kipi (editor stubs + rules)

In your editor, run `/kipi` (or ask the agent to talk to Kipi). On the first start Kipi shows its install plan and **asks before it writes anything** ("First start" above). For each editor you pick:

| Editor | Skill stubs go to | Rules block merged into |
|-----|-------------------|-------------------------|
| Cursor | `.cursor/skills/` | `.cursorrules` |
| Claude Code | `.claude/skills/` | `CLAUDE.md` |
| VS Code Copilot | none | `.github/copilot-instructions.md` |
| Windsurf | none | `.windsurfrules` |
| Cline / Roo | none | `AGENTS.md` |

The rules **content** is the same everywhere: it lives in `agent_files/rules-template.md` and is copied into whichever file your editor uses. Claude Code also gets five command files in `.claude/commands/`.

To re-run or add another editor later, use **[IN] Install** from Kipi's menu.

### Step 3: Register with BMad (only if you use it)

Tell `bmad-help`: "I installed the Kipi module, help me register it."

---

## Usage

### Activate Kipi

In your editor, invoke Kipi: say "talk to Kipi" or use `/kipi`. Kipi loads your settings, shows its install plan if it is not installed yet, checks the vault, and presents a menu:

| Code | What It Does |
|------|-------------|
| RI | Copy the vault skeleton into an empty vault |
| CS (alias CD) | Deep-scan one area of the code and write one chapter |
| UD (alias PD) | Process files dropped in the raw-notes inbox |
| UK | Update knowledge after code changes |
| DI | Run a 10-question developer interview to fill knowledge gaps |
| CL | Audit the vault against its conventions |
| ST | Show knowledge status and coverage |
| IN | Install or repair Kipi for your editors |

`[CD]` and `[PD]` work as `[CS]` and `[UD]`. In Claude Code, `/kipi-update` also runs `/kipi-update-core`; Cursor has no such alias.

### First Time Setup

1. Activate Kipi. If the vault folder is empty it offers **[RI] Repository Initialize**: say yes
2. Kipi copies two files into the vault: `index.md` (the router) and `_meta/conventions.md` (the writing rules), creates the raw-notes folders and the state files of first use
3. Then run **[CS] Code Scan** on the area you need first: it writes the first chapter and adds its row to the router

The sections of the vault are the project's own: there is no fixed list of folders to approve.

### Knowledge Directories

```
<workspace>/
├── knowledge/                       ← the vault ({{VAULT_DIR}})
│   ├── index.md                     (the router: read this first)
│   ├── _meta/
│   │   ├── conventions.md           (the writing rules)
│   │   ├── variants.md              (only with several branch families, if you keep one)
│   │   └── roles-and-sources.md     (the only place names live, if you keep one)
│   └── <the project's own sections>/
├── notes/                           ← the raw-notes inbox ({{RAW_NOTES_DIR}})
│   ├── ready-for-classify/          (extractions waiting to be folded in)
│   └── _archive/<YYYY-MM>/          (originals, append-only)
└── .kipi/state/                     ← Kipi's state ({{KIPI_STATE_DIR}})
    ├── state/<slug>.json            (one file per variant; `default` with one branch family)
    ├── chapters.json, ledger.jsonl, uk-foldins.jsonl, todo.jsonl, vault.json
    ├── note.md
    └── reports/
```

#### Hard rules enforced by Kipi

1. **Names of people** appear only in `_meta/roles-and-sources.md`, when the vault has one. Everywhere else the vault uses role labels.
2. **Sensitive data** never enters the vault, in any form: placeholders only.
3. **Kipi writes only a closed list of paths**: the vault, the extractions and archive of the raw-notes inbox, its state folder and the interview answers file in the inbox; at the install, and only after you say yes, the editor's stubs and commands, the rules block, the install record and the install line of its main skill. It never writes into the code repository.

#### .gitignore

Kipi does not edit `.gitignore`. The raw-notes inbox holds verbatim messages with their authors: keep it out of version control when the vault sits inside the code repository, and decide whether the state folder is versioned.

### Processing User Files

Drop any file into the raw-notes inbox (`notes/` by default): messages, call notes, documents. Then run **[UD]**. Kipi classifies each file, writes a structured extraction in `ready-for-classify/`, archives the original by month and adds a line to its ledger. **[UK]** folds the extractions into the chapters.

### Keeping Knowledge Current

After code changes, run `/kipi-update-core` or other agents will remind you via the Kipi Handshake Protocol of the rules block. `[UK]` applies the update and reports; you review the git diff.

---

## File Structure (This Repo)

```
kipi-agent/
├── agent_files/               # → copy to the Kipi folder (the brain)
│   ├── SKILL.md               # persona, menu, state, activation, the install line
│   ├── rules-template.md      # editor-neutral rules block (written by kipi-install)
│   ├── ide_adapters/          # per-editor skill stubs + commands
│   ├── kipi-install/
│   ├── kipi-*/                # one folder per capability
│   ├── workflows/             # capture.md and one workflow per capability that has steps
│   ├── templates/             # the vault skeleton: index and conventions
│   └── data/                  # the raw-notes classification table
├── workshop/                  # participant prompts (install + agent builder)
├── docs/                      # a drawing of the vault layout
├── START_HERE.md
├── LICENSE
└── README.md
```

---

## Not yet proven

- Agents other than Claude Code (the skills are plain markdown, but only Claude Code has run them).
- A fresh install on a real project, or with another editor. The first start ran with Claude Code on two toy projects installed by onegate. After a yes, the state files, the writes under the editor's folders and to the rules file, and the removal of the install line from the main skill were not stopped by the permission check; after a no, nothing was written and no state folder was created. A permission check that refuses one of those writes has not been met. The copy of the vault skeleton by `[RI]` is written, not exercised.
- The single-branch path: only the multi-branch mechanics ran on a real project, in the keeper this fork generalizes.
- Two keepers working at once with the message protocol of `kipi-update-core`.
- The in-repo layout (vault and state inside the code repository) ran only an install by onegate and a first start answered no, on a toy project; no workflow has run in it. A code repository beside the workspace rather than inside it has not run.
