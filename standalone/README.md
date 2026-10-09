# Kipi, standalone edition

This folder lives on the branch **`onegate`** of this fork. The fork's `main` branch is identical to upstream, [`ladymeyy/kipi-agent`](https://github.com/ladymeyy/kipi-agent); this branch is upstream plus additions only, and everything it adds is under `standalone/` (plus one section at the top of the root `README.md` and one holder line in `LICENSE`).

It is Kipi, the knowledge keeper, as **two Claude Code skills with no BMad requirement**, and a skeleton for the vault it keeps. It is used by [onegate](https://github.com/LairdLuca/onegate), which installs it into a project after a short interview, and it can also be installed by hand (below).

## What is here

```
standalone/
├── README.md                          this file
├── skills/
│   ├── kipi/                          the keeper: persona, menu, state, workflows
│   │   ├── SKILL.md
│   │   ├── workflows/                 update-knowledge, capture, audit-vault, developer-interview
│   │   └── data/raw-notes-classification.csv
│   └── kipi-update/SKILL.md           the light entry: runs the update workflow only
└── vault-skeleton/                    an empty vault: index.md and _meta/conventions.md
```

## How it differs from `agent_files/`

| | `agent_files/` (upstream) | `standalone/` (this branch) |
|---|---|---|
| Requires BMad | yes, it refuses to activate without it | no; BMAD is optional and read only inside a conditional block |
| Shape | a BMad module copied into `_bmad/kipi/`, with per-IDE stubs | two Claude Code skills, copied into `.claude/skills/` |
| Paths | fixed (`knowledge/`, `user-data/`, `_bmad/_memory/`) | placeholders: workspace, code repository, vault, raw-notes inbox, state folder |
| Vault layout | `knowledge/core_knowledge/` and `private_knowledge/`, folder contracts | `index.md` and `_meta/conventions.md` as the contract; the sections are the project's own |
| Where names live | only in `team_and_stakeholders/` | by role label everywhere; names only in `_meta/roles-and-sources.md`, when the vault has one |
| Code on several branch families | not covered | per-variant capture: every file is captured at one commit on one branch, with a reference branch per variant; a single-branch path exists |
| Staleness | not covered | by git ref, on two references, independent of the checked-out branch |
| Several keepers at once | not covered | a message protocol, no waiting and no lock (`kipi-update`) |
| State | one sidecar workflow-state file | a state folder: per-branch state, chapters, ledger, fold-ins, todo, vault values, notes; created on first use |

The capability menu maps like this:

| Upstream code | Here | Note |
|---|---|---|
| RI, initialize | none | replaced by `vault-skeleton/`, copied into an empty vault on the first run |
| CS, code scan | CD | renamed |
| UD, process user data | PD | renamed; processes the raw-notes inbox |
| UK, update | UK | same code |
| DI, developer interview | DI | same code |
| CL, cleanup | CL | same code; an audit that proposes and never fixes on its own |
| ST, status | ST | same code |
| IN, install | none | no per-IDE stubs: the skills are plain Claude Code skills |

## What derives from upstream

The following files derive from upstream and are **modified** (standalone, no BMad, other paths, other rules where listed). Each of them says so in its header.

| File here | Derives from | What changed |
|---|---|---|
| `skills/kipi/SKILL.md` | `agent_files/SKILL.md` | The keeper's skill and persona (name, voice, hard discipline of approval), the capability table, the write permissions. No BMad activation, no IDE stubs; paths as placeholders; a state folder; first-use creation of state files; capture by branch |
| `skills/kipi-update/SKILL.md` | `agent_files/kipi-update-core/SKILL.md` | The update companion. A message protocol for keepers in parallel; it asks no questions |
| `skills/kipi/workflows/update-knowledge.md` | the `[UK]` capability through `kipi-update-core` | Apply then report; what is never applied unasked; the fold-in record |
| `skills/kipi/workflows/developer-interview.md` | `agent_files/workflows/developer-interview/` | The interview. A gap rubric read from the vault's own signals; answers are indications to validate |
| `skills/kipi/workflows/audit-vault.md` | `agent_files/kipi-cleanup-knowledge/` | The audit. Three passes against this vault's conventions instead of upstream's folder contracts |
| `skills/kipi/data/raw-notes-classification.csv` | `agent_files/data/knowledge-classification.csv` | A source-status column that applies the channel rule of the conventions; no domain rows |

The idea of an initialize step comes from upstream's RI capability; here it is the `vault-skeleton/` folder. `skills/kipi/workflows/capture.md`, `vault-skeleton/` and this README are new writing.

## Install by hand, without onegate

1. Copy `skills/kipi/` and `skills/kipi-update/` into `<workspace>/.claude/skills/`.
2. Copy `vault-skeleton/` into the folder you chose for the vault (an empty folder).
3. Replace the placeholders below in the copied files, and keep or remove each conditional block.
4. Check: no `{{` and no `onegate-if` or `onegate-endif` is left in the copied files.
5. In Claude Code, started in the workspace, say "talk to Kipi".

**Placeholders** (written with double braces in the files):

| Placeholder | Meaning | Default for standalone use |
|---|---|---|
| `{{PROJECT_NAME}}` | Project name | name of the code repository folder |
| `{{WORKSPACE}}` | Absolute path of the workspace, forward slashes | the folder where you start Claude Code |
| `{{CODE_REPO}}` | Absolute path of the code repository | the git repository in or below the workspace |
| `{{VAULT_DIR}}` | The vault | `{{WORKSPACE}}/knowledge` |
| `{{RAW_NOTES_DIR}}` | The raw-notes inbox, with `ready-for-classify/` and `_archive/` inside | `{{WORKSPACE}}/notes` |
| `{{KIPI_STATE_DIR}}` | Kipi's state folder | `{{WORKSPACE}}/.kipi` |
| `{{ONEGATE_DIR}}` | Where `onegate-answers.md` would be, if onegate is used | `{{WORKSPACE}}/.onegate` (the file may not exist) |
| `{{ARTIFACTS_DIR}}` | Where test round reports go | `{{WORKSPACE}}/artifacts` |
| `{{CONVERSATION_LANGUAGE}}`, `{{DOCUMENT_LANGUAGE}}` | Languages of the conversation and of the vault | English, English |
| `{{DATA_APPROVER}}` | Role that approves writes to shared data | the human who runs the session |
| `{{BASELINE_VARIANT}}`, `{{VARIANT_RULE}}`, `{{VARIANT_REFERENCE}}` | Baseline variant; how a branch name maps to a variant; the reference branch of a variant | only with several branch families; for example the prefix before the first `/`, and `origin/<variant>/develop` |

**Conditional blocks.** A part of a file that holds only for some answers is wrapped between two marker lines. In markdown: `<!-- onegate-if: <condition> -->` and `<!-- onegate-endif -->`. Keep the content and delete the two marker lines when the condition holds; delete the whole block when it does not. Blocks do not nest. The conditions used in this folder are `multi-branch` (the same code is kept in several long-lived branch families, one per client or edition), `single-branch` (the opposite) and `bmad` (BMAD is installed in the project).

Without a multi-branch answer, a project with one branch family keeps the `single-branch` blocks and has no variant files.

## Not yet proven

- Agents other than Claude Code (the skills are plain markdown, but only Claude Code has run them).
- A fresh install on an empty project: the first-use creation of the state files and the copy of the vault skeleton are written, not exercised.
- The single-branch path; only the multi-branch mechanics ran on a real project, in the original keeper that this edition generalizes.
- Two keepers working at once with the message protocol of `kipi-update`.
- The in-repo layout (vault and state inside the code repository), and a code repository beside the workspace rather than inside it. The original ran with the vault in a workspace that holds the code repository as a nested repository.

## Credits and license

Kipi, its name, the capability menu and the shape of the keeper come from [`ladymeyy/kipi-agent`](https://github.com/ladymeyy/kipi-agent) (MIT). The upstream author is credited here with thanks. The license is MIT: the upstream notice is kept unchanged in the root `LICENSE`, with one holder line added for the additions of this branch.
