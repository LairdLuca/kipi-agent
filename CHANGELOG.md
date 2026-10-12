# Changelog

This file lists the versions of `LairdLuca/kipi-agent`, a fork of `ladymeyy/kipi-agent` that carries the author's modifications on its `main` branch, newest first. The versions are the fork's own, not upstream's, and each one is a git tag named `onegate-v<version>`, made on `main`. Each entry says what the fork changes compared with upstream's `main`, and every item can be checked with `git diff upstream/main..main`, with the upstream repository added as a remote named `upstream`. The modifications were developed on a branch `onegate`, which was merged into `main` by pull request #1 and deleted.

## 1.1.1

Where Kipi lives. The modifications of 1.1.0 were merged into `main` of this fork by pull request #1, and the branch they were developed on, `onegate`, was deleted. No workflow of Kipi changes: the texts follow. The tag `onegate-v1.1.0` stays on the commit it was made on, an ancestor of `main`.

### Where Kipi lives

- **`main` carries the modifications.** `main` of this fork now differs from upstream's `main`. There is no other branch to clone or to switch to, and the tag `onegate-v1.1.1` is made on `main`.
- **The modification line.** The header line of each modified file reads "Modified in `LairdLuca/kipi-agent`" in place of the line that named the branch; in the words after the colon, "this branch" became "this fork" (`README.md`, `START_HERE.md`, `agent_files/SKILL.md`, `agent_files/rules-template.md`, `agent_files/templates/index-template.md`), and the rest is unchanged. onegate's install checks this line.
- **The repair message of the brain.** `agent_files/SKILL.md` and `agent_files/kipi-install/SKILL.md` tell the person to copy `agent_files/` from `LairdLuca/kipi-agent`, without naming a branch.
- **"This fork" in the brain.** `agent_files/SKILL.md` and `agent_files/workflows/repository-initialize/workflow.md` say "the keeper this fork generalizes".

### Documentation and workshop

- **README.** The section is "About this fork", the column of the table is "this fork's `main`", the version line says 1.1.1, and no sentence presents `main` as a copy of upstream.
- **`START_HERE.md`.** It says that the workshop installs this fork's `main`, not upstream's own Kipi.
- **The workshop clones `main`.** `workshop/2-INSTALL-PROMPT.txt` runs `git clone --branch main` of the fork, and its ZIP fallback downloads `main.zip`, which unzips to `kipi-agent-main`.
- **`LICENSE`.** The added holder line names the fork, `LairdLuca/kipi-agent`, in place of the branch. The upstream notice is unchanged.
- **This changelog.** The 1.1.0 entry says "the fork" and "this fork" where it said "the branch" and "this branch"; its facts are unchanged.

### Not yet proven

- The workshop install prompt has not been run with the new clone command and ZIP address.

## 1.1.0

The first tagged version. Compared with upstream's `main` (commit `5b957a0`), the fork changes the following.

### Requirements and install

- **BMad is optional.** Upstream's activation stops when `_bmad/` is missing (`agent_files/SKILL.md`, "On Activation", step 1). In this fork the activation does not check for `_bmad/`; BMad's configuration is read only inside conditional blocks that apply when BMad is installed (`agent_files/SKILL.md`, `agent_files/kipi-install/SKILL.md`).
- **The install asks first.** Upstream's activation runs `kipi-install` silently and prints a one-line confirmation. In this fork the main skill carries a line that begins `KIPI-NOT-INSTALLED:`; while it is there, the activation shows the install plan before the menu and writes nothing until the answer. `[A]` applies, `[E]` edits the list of editors, `[N]` writes nothing and leaves the line in place. After a successful install the line is deleted as the last action (`agent_files/SKILL.md`, `agent_files/kipi-install/SKILL.md`).
- **An existing stub or command is never overwritten.** The plan lists it as "exists, kept" (`agent_files/kipi-install/SKILL.md`).
- **Paths are placeholders.** Upstream's fixed paths (`knowledge/core_knowledge/`, `user-data/`, `_bmad/_memory/kipi-sidecar/`) are replaced by placeholders for the workspace, the code repository, the Kipi folder, the vault, the raw-notes inbox and the state folder. The placeholders and the way to fill them by hand are in the README.

### Vault and menu

- **A different vault contract.** Upstream's `core_knowledge/` and `private_knowledge/` with folder contracts give way to an `index.md` that routes and a `_meta/conventions.md` that holds the writing rules; the sections of the vault are the project's own. `agent_files/templates/conventions-template.md` is added and `agent_files/templates/index-template.md` is changed.
- **Names of people live in one place.** Upstream allows them in `team_and_stakeholders/`. In this fork they appear only in `_meta/roles-and-sources.md`, when the vault has one, and role labels are used everywhere else (`agent_files/SKILL.md`, "Principles").
- **`[RI]` copies a skeleton.** Upstream's `[RI]` scans the repository and proposes a folder structure. In this fork it copies two files, `index.md` and `_meta/conventions.md`, into an empty vault and creates the folders and state files of first use (`agent_files/kipi-repository-initialize/SKILL.md`).
- **Aliases.** `[CD]` and `[PD]` work as `[CS]` and `[UD]` (`agent_files/SKILL.md`), and `/kipi-update` is added as an alias of `/kipi-update-core` for Claude Code (`agent_files/ide_adapters/claude-code/commands/kipi-update.md`).
- **29 files of upstream's vault are removed**: the step files of the four workflows (11), the four checklists, `data/batching-config.yaml`, `schemas/workflow-state-schema.json` and twelve templates. The workflow files that remain (`workflows/*/workflow.md`) hold the steps themselves.

### Capture, state and update

- **Capture by git branch.** `agent_files/workflows/capture.md` is added. A vault file is captured at one commit of one branch, its sources are hashed as git blobs read at that commit (`hash_method: git-blob`), and a source on another branch is read with `git show <ref>:<path>` without switching branch. Code kept in several long-lived branch families is handled by variants, each with a reference branch and its own variant files; a single-branch path exists (`agent_files/workflows/capture.md`).
- **A state folder of its own.** Upstream keeps a sidecar under `_bmad/_memory/kipi-sidecar/` with `processed-files.md`, `instructions.md` and one `workflow-state.json`. In this fork the state folder holds one state file per variant (`state/<slug>.json`), `chapters.json`, `ledger.jsonl`, `uk-foldins.jsonl`, `todo.jsonl`, `vault.json`, `note.md` and `reports/` (`agent_files/SKILL.md`, "State layout").
- **`[UK]` applies, then reports.** Upstream's `[UK]` drafts a proposal and waits for `[Y]es`, `[N]o` or `[M]odify`. In this fork it asks no questions and the review is the git diff, which stays uncommitted (`agent_files/kipi-update-core/SKILL.md`).
- **Keepers in parallel.** Several keepers may work at once, with no waiting and no lock: a keeper announces what it touches, answers announcements, and re-reads a file right before writing it. A keeper finds the others with `herdr agent list` and sends each a message that starts with `KIPI <name>:`; without herdr only the re-read rule remains (`agent_files/kipi-update-core/SKILL.md`).
- **The existence check of `[UK]` is a tool call.** Before step 1 of the workflow, the paths named under "Before starting" are looked at with a `Glob`, an `ls` or a `Read`, whether or not the run will need them, and a missing one stops the run with a report (`agent_files/kipi-update-core/SKILL.md`).

### Other workflows

- **`[CS]`** keeps its steps in one workflow file, writes one chapter per run, captures by git branch and declares oversized sources (`agent_files/workflows/code-scan-document/workflow.md`).
- **`[UD]`** reads the raw-notes inbox (`ready-for-classify/`, `_archive/`), keeps a ledger and an append-only archive, and gives each extraction a source status that depends on the channel. `agent_files/data/knowledge-classification.csv` is rewritten with a `source_status` column (`agent_files/workflows/process-user-data/workflow.md`).
- **`[DI]`** reads its gap rubric from the vault's own signals, writes nothing into the vault, and treats answers as indications to validate, handing them to the raw-notes pipeline (`agent_files/kipi-developer-interview/SKILL.md`).
- **`[CL]`** audits the vault against its own conventions in three passes (structure, rules, hygiene) and makes proposals only (`agent_files/kipi-cleanup-knowledge/SKILL.md`).
- **`[ST]`** evaluates staleness by git ref instead of showing a "Last Updated" timestamp per chapter, fits on one screen and writes nothing (`agent_files/kipi-knowledge-status/SKILL.md`).

### Editors and rules

- **Stubs, commands and READMEs.** The 16 editor skill stubs, the 4 existing editor commands and the 4 editor READMEs under `agent_files/ide_adapters/` name the Kipi folder in place of upstream's `_bmad/kipi/`. The `kipi-update-core` stubs and command load the skill without the Kipi persona, and the READMEs of the editors that have no skill folder say that the first start is asking the agent to read the main skill, which shows the install plan and asks before writing.
- **Rules block.** `agent_files/rules-template.md` looks for the vault's `index.md` instead of `knowledge/core_knowledge/index.md` and states a closed list of paths Kipi writes to.

### Documentation and license

- **README.** A section about the fork at the top (named "About this branch" until 1.1.1), the table of differences, the install by hand with its placeholders, the first start, the list of files changed, and credits to upstream.
- **`START_HERE.md` and `workshop/`.** The workshop installs this fork, with BMad as an option. `workshop/2-INSTALL-PROMPT.txt` clones this fork.
- **`LICENSE`.** Upstream's copyright line is kept unchanged and one line is added for the additions of this fork.
- **A modification line.** Each file whose content changed carries one line after its title that says it was modified in this fork and how; the files without it are listed in the README, "Files changed".

### Not yet proven

Each skill that has one says so in its own "Not yet proven" section (`agent_files/SKILL.md`, `agent_files/kipi-update-core/SKILL.md`, `agent_files/workflows/capture.md`). In short, at this version:

- The first start ran with Claude Code on two toy projects installed by onegate, once answered yes and once no: after the yes the state files, the writes under the editor's folders and to the rules file, and the removal of the install line went through; after the no nothing was written. It has not run on a real project or with another editor, and the copy of the vault skeleton by `[RI]` has not run (onegate's install had already copied it).
- The single-branch path has not been run on a real project.
- No pair of keepers has used the parallel protocol at once.
- The existence check of `[UK]` ran in one fresh session, which looked at the missing path and stopped with its report. How reliably a session makes that call, and the case where `[UK]` has a chapter to update, have not been run.
