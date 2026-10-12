---
name: kipi-update-core
description: Companion skill for Kipi. Use after another agent makes code changes in the code repository, before a /clear or the closing of a tab that produced knowledge, or whenever the user asks to update the knowledge vault, sync Kipi, or check what is stale in the vault. Runs Kipi's Update Knowledge workflow without activating the full Kipi persona.
---

# Kipi: Update Knowledge (light entry)

> Modified on the branch `onegate` of `LairdLuca/kipi-agent`: keepers in parallel by messages, applies then reports, no questions asked.

This skill runs the `[UK]` workflow of Kipi without loading the persona, the menu and the full state. It serves whoever has just finished a piece of work (a development agent, a tab of herdr (<https://herdr.dev>, a terminal workspace manager) before a `/clear`, the human in chat) and needs what emerged to reach the vault.

**Detect, apply, report. It asks no questions.** The review happens afterwards, on the git diff, which stays uncommitted.

## Before starting

- `{{VAULT_DIR}}/index.md` must exist. If it is missing, stop and say so: the vault is not in a state to work on.
- If a file this skill needs is missing (`{{KIPI_HOME}}/workflows/capture.md`, `{{VAULT_DIR}}/_meta/conventions.md`, or a stub or command), stop, say which path is missing and that `[UK]` needed it, and propose the repair of the rule "Missing files" of `{{KIPI_HOME}}/SKILL.md`. Write nothing for the repair until the person says yes; never reconstruct the missing text.
- **Other keepers at work?** There is no waiting and no lock: the protocol "Keepers in parallel" below applies.
- Write only inside Kipi's closed list of paths (Kipi's rules block in the workspace rules file, or the default list of the `kipi` skill). Never change the code of `{{CODE_REPO}}`: the closed list is the only place you write, wherever it sits.

## Keepers in parallel

**Keepers in parallel, with no waiting and no lock** (decided after the first real run of the work-item pipeline, where a keeper's update was postponed because another keeper was running: the keepers exchange messages to find out whether they are trampling each other). Several keeper sessions may work together on the workspace. They coordinate like this:

1. **Announcement.** As soon as you know which chapters and which state files you will touch (end of step 1 of the workflow), look at `herdr agent list`: the `working` agents with "kipi" in their name or label are the other keepers. To each send `herdr agent prompt <name> "KIPI <your name>: I am about to touch <chapters>, <state files>. What do you touch?"`. Do not wait for the answer: keep working. With no other keepers, no messages.
2. **Reply.** When you receive a message that starts with `KIPI <name>:` from another session, answer at once the same way with your own files, then resume where you were.
3. **Shared chapter.** Whoever announced it first keeps it. The other does everything else first; when it reaches that chapter it re-reads it from disk and writes only its own additions, without rewriting sections that are not its own. If the two changes fall on the same section, the second puts its own at the bottom of its own outcome as a proposal and says so in the closing message.
4. **Shared state files** (`uk-foldins.jsonl`, `ledger.jsonl`, `vault.json`, `chapters.json`, `state/<slug>.json`, `note.md`): everybody touches them, and the rule of the `kipi` skill ("Conflicts are intended") applies: re-read the file right before writing it, change only your own lines or keys, never regenerate it from memory, and after writing re-read it to check that the others' lines are still there. The identifier `uk-<date>-<n>` is chosen at the moment of writing by re-reading `uk-foldins.jsonl` (highest plus one), not at the start; if after writing it turns out to be doubled, renumber your own.
5. **Closing.** One message to the other keepers still `working`: what you wrote and with which identifier.
6. **Nobody answers?** No waiting: points 3 and 4 apply. A message reaches the other session as a prompt while it works, and Claude Code queues it for the turn in progress: the answer may take a few minutes.

Without herdr (`herdr agent list` fails) there is nobody to message: only points 3 and 4, and above all the rule of re-reading a file right before writing it, remain.

## What to do

1. Run the Workflow part below from start to end. It is the only home of the workflow: this skill does not repeat its steps.
2. When that workflow asks for it, read `{{KIPI_HOME}}/workflows/capture.md` (hash, stale, variant files) and `{{VAULT_DIR}}/_meta/conventions.md` (writing rules).
3. Do not activate the `kipi` skill: neither the greeting nor the menu is needed.

## What the mandate says

The message that invokes the skill can change four things. If it says nothing, the defaults apply.

| The mandate says | Effect | Default |
|---|---|---|
| which files to read as a source (outcomes of tabs, test round reports, handoffs) | they are the "session" signal of step 1, in place of the conversation | the current conversation and the files modified in `{{CODE_REPO}}` |
| who asked or authorized, and when | enters verbatim in the `mandate` field of the fold-in | `post-development hook` |
| not to apply (`proposals only`, `do not apply`, `answer none`, "the authorization is not there yet") | the "proposals only" mode of the workflow: no writes into the vault or the state, proposals handed over where indicated | apply |
| a named structural operation (split a mixed chapter, move a section) | becomes applicable; without this line it stays among the "To decide" items | propose only |

## At the end

The report of step 5 of the workflow, in chat or in the outcome file the mandate names. No commit. If there was nothing to update, one line.

## Workflow: [UK] Update Knowledge

The single home of the workflow: it is read both by Kipi (the `[UK]` entry of the menu) and by the skill `kipi-update-core`. Goal: keep `{{VAULT_DIR}}` aligned with the code, with the extractions ready to fold in and with what emerged in the session.

**`[UK]` applies, then reports.** It does not ask for approval before writing: the review happens afterwards, on the git diff, which stays uncommitted until the human gives the OK to commit. This holds for `[UK]` only: `[CS]`, `[UD]`, `[DI]` and `[CL]` keep their stops.

**Several keepers may run at once.** There is no wait and no lock. If another keeper is working, follow the protocol "Keepers in parallel" of this skill, above (announce what you touch, answer announcements, re-read before writing). Never stop to wait for another `[UK]` to finish.

Before writing into a chapter read `{{VAULT_DIR}}/_meta/conventions.md` (writing rules) and `{{KIPI_HOME}}/workflows/capture.md` (hash, stale, variant files).

**If the mandate is a migration** (split a mixed chapter, bring a file to the current capture format) there are no new facts to collect: skip steps 1 and 2, work with `capture.md` section 4 (and section 7 for a split), and resume here at step 4.

---

### 1. Collect the signals

Three sources, read in this order of cost.

1. **Session.** Facts that emerged in this conversation or in the files the mandate names (outcomes of tabs, test round reports, handoffs): what changed in `{{CODE_REPO}}`, what was measured, what a person said. This includes **who people are**: a role, a change of team, a new name go into `_meta/roles-and-sources.md` when the vault has it, which no hash check can flag because it has no sources.
2. **Ready extractions.** `{{RAW_NOTES_DIR}}/ready-for-classify/*.extracted.md`, grouped by `target_chapter`.
3. **Code drift.** Only for the chapters **in play**: those that list among their `source_files` a file touched by the work just done, plus those the mandate names. Evaluate staleness as in `capture.md` section 3. A check over the whole vault is `[ST]` or `[CL]`, not this.

To find the chapters in play start from `{{VAULT_DIR}}/index.md` and a Grep of the source path over the frontmatters. Do not load chapters that are not involved.

### 2. Decide where each fact goes

- **Status of the information** per `conventions.md` section 1.1: code read is a fact, a chat is an indication to validate, a database measurement declares environment and date. The status is written into the chapter and is not lost in the passage.
- **Destination file**: the chapter that describes the topic.
<!-- onegate-if: multi-branch -->
  A fact read on one variant goes into the file of that variant (`capture.md` section 5); if its variant file does not exist, it is created.
<!-- onegate-endif -->
- **Source**: every added line carries `file:line` plus commit, or who and when, or the report and its section.

### 3. Apply

Targeted `Edit`s. A whole-chapter rewrite only if the mandate asks for it.

After modifying a file:

- if you re-read its sources **on today's reference** and aligned the text, recapture (`capture.md` section 4). If you re-read them **at the capture commit** to verify or correct a citation, the capture stays where it is and the line numbers stay those of that commit. If you did not re-read them, the capture is not touched;
- update `last_updated`;
- the head line of `index.md` ("Last `[CS]/[UK]/[CL]`") is not updated: the history lives in `uk-foldins.jsonl`. `_meta/sources.md`, when the vault has it, is touched only when you create a file or change the list of its sources;
- a folded extraction leaves `{{RAW_NOTES_DIR}}/ready-for-classify/`, and its line in `ledger.jsonl` gets `record_type: folded` and `folded_into` (edit the line, do not append one);
- if the status of a chapter changes, update its row in `{{VAULT_DIR}}/index.md`, the counters of the "Progress" table of the same `index.md` (recount the rows, do not apply the bare change: if they were behind, this makes them true again) and `chapters.json`.

#### What is never applied unasked

These items are not written into the vault without a mandate that names them: they go into the report, section "To decide".

| Case | What to do instead |
|---|---|
| A non-code source (chat, call, email) contradicts a chapter | Open a communication-log line in the chapter (`conventions.md` section 12), state `awaiting answer`; the contradicted text is not rewritten |
| The code contradicts a chapter | Apply it: the code wins. This is the normal case of `[UK]` |
| Split, move, rename or delete a chapter or a section | Propose it. Splitting a mixed chapter (multi-branch projects) follows `capture.md` section 7 and needs the mandate |
| Modify `_meta/conventions.md` | Propose it: it is the constitution of the vault |
| A fact whose variant or source cannot be established | Ask, or leave it out and say so. Do not write `(inference, to be verified)` to close in a hurry |
| Sensitive data (`conventions.md` section 4) | Never, in any form: placeholder |
| Corrections that came out of a `[CL]` audit | They stay with `[CL]`, which proposes and does not fix on its own |

#### "Proposals only" mode

If the mandate says not to apply (`proposals only`, `do not apply`, `answer none`, or states that the human's authorization is not there yet), run steps 1 and 2, **write nothing into the vault or into the state**, and hand over the proposals where the mandate says (normally at the bottom of the tab's outcome file). Each proposal: destination file, text to add, source.

### 4. Record

One new line in `uk-foldins.jsonl`:

```json
{"id": "uk-<YYYY-MM-DD>-<n>", "date": "<YYYY-MM-DD>", "branch": "<git branch> @ <commit>", "chapters": ["<path>: <what, in one line>"], "mandate": "<who asked or authorized, and when>", "sources": ["<files read>"]}
```

`<n>` is the day's sequence number: choose it at the moment of writing, by re-reading the last lines of the file (highest plus one), not at the start. The `mandate` field quotes the source of the assignment as it arrives ("the human, chat, `<date>`", "authorization at the gate, `<date>`"); if `[UK]` starts by itself at the end of a task, `mandate: "post-development hook"`. These are the minimum fields; the ones already in use in existing lines (`commit`, `task`, `workflow`, `correction`) stay allowed.

Then update the state file of the checked-out branch, `{{KIPI_STATE_DIR}}/state/<slug>.json` (last workflow, open items; the slug is `default` in a project with one branch family). If the round touched files of other variants, their open items are noted there, with the branch next to them. The exception is a multi-round job that already has its open items in a state file: later rounds continue there even if the checkout changed in the meantime, so the open items of one job do not scatter. Only if they changed, update `chapters.json` and the counters of `vault.json` (variant files do not count as chapters: `capture.md` section 6). Write only the state files you touched, keeping their line endings. **Re-read a state file right before modifying it**: other sessions work on the same repository.

The history lives here and in `note.md`. **Do not add `resync_note_*` or other prose to the frontmatter of the chapters.**

### 5. Report

The report is what makes the later review possible. In chat, or in the outcome file if the mandate names one:

```
🧠 [UK] <id>  ·  branch <git branch> @ <commit>

Applied (N)
- <chapter>: <what changed, one line in domain words>  ·  source: <...>

Variant files created (N)
- <path>

To decide (N)
- <case>: <why it was not applied>  ·  <what is needed to close it>

Still stale after this round (N)
- <chapter>: <source that changed>

To review: git -C "{{WORKSPACE}}" diff -- "{{VAULT_DIR}}" "{{KIPI_STATE_DIR}}"   (when the workspace is under git; otherwise review the files named above)
```

Empty sections are omitted. If there was nothing to apply, one line says so and no fold-in is written.

`[UK]` **does not commit.** The commit of the workspace is decided by the human.

---

## Common mistakes

| What happens | Remedy |
|---|---|
| The skill waits for another keeper to finish | Never wait: announce, do the rest, re-read before writing |
| The skill asks the user questions | It asks none: what cannot be decided goes to "To decide" in the report |
| The skill changes code in `{{CODE_REPO}}` | Never: it writes only inside the keeper's closed list |
| The `kipi` persona is loaded for a small update | Do not activate it: this is the light entry |
| The mandate says "proposals only" and the vault is changed anyway | Proposals only means no write into the vault and none into the state |
| The capture is moved after editing text without re-reading the sources | Capture only after re-reading the sources on the reference; otherwise leave the capture alone |
| A fold-in id is chosen at the start and collides at the end | Choose the id at the moment of writing, from the current last line |
| Prose is appended to a frontmatter ("resync note") | History goes to `uk-foldins.jsonl` and `note.md` |
| A contradiction from a chat is written over the chapter | Open a communication-log line, state `awaiting answer`, report it under "To decide" |

These restate rules of the original keeper (its workflows, its state rules and its vault conventions) as mistakes to avoid.

## Not yet proven

- No pair of keepers has used the parallel protocol yet: it was written after the first real run of the work-item pipeline, where a keeper's update was postponed because another keeper was running, and never exercised by two sessions at once. The messages go through herdr; without it only the re-read-before-write rule is available.
- The single-branch path (no variants) has not been run on a real project.

## Checklist

- [ ] Every added line has its source and its status (section 1.1 of the conventions)
- [ ] No fact of one variant ended up in the file of another
- [ ] No hash was written without `rev-parse` at the declared commit; no capture was touched without re-reading the sources
- [ ] No new prose in a frontmatter
- [ ] The items of "What is never applied unasked" are in the report, not in the vault
- [ ] One line in `uk-foldins.jsonl` with a unique `id` and a `mandate`
- [ ] State files re-read before the edit, and only the necessary ones touched
- [ ] No commit
