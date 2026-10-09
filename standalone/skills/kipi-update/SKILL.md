---
name: kipi-update
description: Companion skill for Kipi. Use after another agent makes code changes in the code repository, before a /clear or the closing of a tab that produced knowledge, or whenever the user asks to update the knowledge vault, sync Kipi, or check what is stale in the vault. Runs Kipi's Update Knowledge workflow without activating the full Kipi persona.
---

# Kipi: Update Knowledge (light entry)

> Derived from the `kipi-update-core` skill of kipi-agent (ladymeyy, MIT); modified: standalone, no BMad, keepers in parallel by messages, no questions asked.

This skill runs the `[UK]` workflow of Kipi without loading the persona, the menu and the full state. It serves whoever has just finished a piece of work (a development agent, a herdr tab before a `/clear`, the human in chat) and needs what emerged to reach the vault.

**Detect, apply, report. It asks no questions.** The review happens afterwards, on the git diff, which stays uncommitted.

## Before starting

- `{{VAULT_DIR}}/index.md` must exist. If it is missing, stop and say so: the vault is not in a state to work on.
- **Other keepers at work?** There is no waiting and no lock: the protocol "Keepers in parallel" below applies.
- Write only inside Kipi's closed list of paths (the section "Knowledge keeper" of the workspace `CLAUDE.md`, or the default list of the `kipi` skill). Never change the code of `{{CODE_REPO}}`: the closed list is the only place you write, wherever it sits.

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

1. Read `{{WORKSPACE}}/.claude/skills/kipi/workflows/update-knowledge.md` and run it from start to end. It is the only home of the workflow: this skill does not repeat its steps.
2. When that workflow asks for it, read `{{WORKSPACE}}/.claude/skills/kipi/workflows/capture.md` (hash, stale, variant files) and `{{VAULT_DIR}}/_meta/conventions.md` (writing rules).
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

## Common mistakes

| What happens | Remedy |
|---|---|
| The skill waits for another keeper to finish | Never wait: announce, do the rest, re-read before writing |
| The skill asks the user questions | It asks none: what cannot be decided goes to "To decide" in the report |
| The skill changes code in `{{CODE_REPO}}` | Never: it writes only inside the keeper's closed list |
| The `kipi` persona is loaded for a small update | Do not activate it: this is the light entry |
| The mandate says "proposals only" and the vault is changed anyway | Proposals only means no write into the vault and none into the state |

These restate rules of the original keeper (its workflows, its state rules and its vault conventions) as mistakes to avoid.

## Not yet proven

No pair of keepers has used the parallel protocol yet: it was written after the first real run of the work-item pipeline, where a keeper's update was postponed because another keeper was running, and never exercised by two sessions at once. The messages go through herdr; without it only the re-read-before-write rule is available.
