# Workflow `[DI]`: Developer Interview

Origin: adapted from `kipi-agent` (ladymeyy, MIT), workflow `developer-interview`; modified: standalone, no BMad.
Rewritten on the signals of this vault: the gap rubric and the rules on answers are NOT upstream's.

Goal: close the knowledge gaps that **are not in the code** but in somebody's head (the senior developer, the project manager, the customer contact). Kipi generates 10 targeted questions, collects the answers and brings them back into the vault **through the existing pipeline** (`{{RAW_NOTES_DIR}}/` → `[PD]` → `ready-for-classify/` → `[UK]`), never by direct write.

---

## Non-negotiable rules

1. **An answer given in chat or by voice is not a fact.** Section 1.1 of `_meta/conventions.md` applies: it enters as an *indication to validate*, with channel and date. It becomes a fact only after verification in source. An answer that contradicts a chapter **does not rewrite it**: it opens a communication-log line (section 12) and stays open.
2. **People are recorded by role label.** Upstream keeps names in one folder; this vault keeps them in one file, `_meta/roles-and-sources.md`, when the vault has it. Answers carry the role label of who answered, the channel and the date, which is what lets someone rebuild on which basis a decision was taken (section 12). Never write a proper name into a chapter.
3. **Kipi does not write into `{{VAULT_DIR}}` from this workflow.** The only direct output is the raw answers file in `{{RAW_NOTES_DIR}}/`. The fold-in goes through `[PD]` and `[UK]` like any other source.
4. **No question is mandatory.** "skip" is a valid answer and is not asked again.
5. **No questions the code answers.** If the answer is found by reading a file, the question is wrong: read the file.

---

## Step 1: Gap analysis

Walk `{{VAULT_DIR}}` and score every chapter. The vault already declares its own gaps: the rubric reads them, it does not guess them.

| Signal (where it is read) | Weight |
|---|---|
| `status: placeholder` in the frontmatter | +0.40 |
<!-- onegate-if: multi-branch -->
| `complete (baseline view)` in `index.md`: chapter written without checking out the variant's branch | +0.35 |
<!-- onegate-endif -->
| The chapter declares it read a source **only as signatures** or "internal logic not read" | +0.30 |
| `status: stale` or `source_files_hashes` diverging from the code | +0.30 |
<!-- onegate-if: multi-branch -->
| A `(not verified)` row in the `## Variants` table (section 3 of the conventions) | +0.30 (cap, not per row) |
<!-- onegate-endif -->
| Open TODO in `{{KIPI_STATE_DIR}}/todo.jsonl` (`status: open`) or in the `_summary.md` of the section | +0.25 |
| Open item in `{{KIPI_STATE_DIR}}/state/<slug>.json` → `open_items` | +0.25 |
| Communication-log line with `**State:** awaiting answer` older than 7 days | +0.25 |
| A statement marked `(inference, to be verified)` (section 1.1) | +0.20 |
| A decision recorded without the **why** (what was decided, not on which constraint) | +0.20 |
| `last_updated` over 90 days old **and** sources in scope modified | +0.20 |

Sum per chapter, cap at 1.0. Sort, keep the first 10, **diversify across sections** (not all 10 from the same one).

**Questions already queued.** Before reading anything from `{{KIPI_STATE_DIR}}/todo.jsonl`, **filter on `status` and on `kind`**. The file has one line per item with a unique key: there are no chains to resolve, but there are two filters not to skip. On the first, only `open` is interview material: a closed item, one waiting for somebody else's answer or one downgraded to a reminder is not. On the second, the vault backlog (`kind: vault-backlog`: things to investigate or document, never started) **is not interview material**: it is the keeper's work, not knowledge in somebody's head. A closed question that reappears in an interview is a reading error, not a knowledge gap.

After that, the items with `di_candidate: true` that are **still current** carry a `question` field already worded: they are points somebody decided to ask and that only wait for an interview. They enter the list **without being re-derived** and take places among the 10, first on equal score. If there are more than 10, show the excess to the user and have them say which to keep: do not drop them silently. When one is answered, **edit its line** in `todo.jsonl`, setting `status` to `closed` and noting on the same line when and by whom the answer came: the file has one line per `id`, do not append a second.

For each one record: `id`, `chapter`, `gap_type` (`baseline-view` | `logic-not-read` | `stale` | `inference` | `open-todo` | `unanswered-log` | `missing-rationale`), `score`, `why` (1 or 2 sentences), `likely recipient` (the role that can answer: senior developer or reviewer, project manager, customer contact, or the human).

Show the table and **stop**. The user can approve, remove, replace or ask for another ranking.

---

## Step 2: Generate the 10 questions

One question per gap, exactly 10. Short (1 to 3 sentences), one thing only, and it says **where the answer will end up**.

| Gap type | Question pattern |
|---|---|
| `baseline-view` | "On `{variant}`, is `{concept}` identical to the baseline or does it diverge? If it diverges, in which project or path does it live?" |
| `logic-not-read` | "In `{class}`, I read `{method}` only as a signature: what does it really do, and which edge cases does it handle that the signature does not show?" |
| `stale` | "In `{chapter}` the code changed: what changed **as a decision**, beyond what the diff shows?" |
| `inference` | "I had deduced that `{hypothesis}` starting from `{source}`. Is that so, or am I missing a piece?" |
| `open-todo` | "`{todo}`: is it still open, superseded, or somebody's job?" |
| `unanswered-log` | "On `{date}` I forwarded `{question}` and no answer came: do I raise it again or has it lapsed?" |
| `missing-rationale` | "Why `{choice}` rather than `{alternative}`? Is there a constraint, a port or a history behind it?" |

Always adapt, do not copy the patterns literally.

When presenting the questions, **announce the two ways of answering and the possibility to skip**:

> You can answer here in chat, numbering (`1. ...`, `2. ...`), or leave a file of answers (call notes, a transcript, an exported email thread) in `{{RAW_NOTES_DIR}}/` and tell me its name: I process it with `[PD]`. No question is mandatory: write "skip" and I move on.

Stop and wait.

---

## Step 3: Collection

Record for every question: `answer` | `skipped`, the **channel** (chat, call, email, work item) and the **date**, plus the role label of who answered.

The raw answers go in a single file:

```
{{RAW_NOTES_DIR}}/developer-interview-<YYYY-MM-DD>.md
```

with the provenance header (role label, when, channel) and the verbatim answers, one per question. If the source was already a file uploaded by the user, that file **is** the original: do not copy it again, refer to it.

No line of this file enters `{{VAULT_DIR}}` in this step.

---

## Step 4: Hand-over to `[PD]` and `[UK]`

Do not integrate from here. Summarise for the user:

- N answers collected, M skipped
- the raw file produced in `{{RAW_NOTES_DIR}}/`
- for every answer, the expected destination chapter and the **status** it will have under section 1.1 (`indication to validate` almost always; `verified fact` only if the answer was already checked in source during the interview)
- the answers that **contradict** the vault: list them apart, because each one becomes a communication-log line and an open item, not a rewrite

Then propose `[PD] Process Raw Notes` on the file just written. End of the workflow.

---

## Common mistakes

| What happens | Remedy |
|---|---|
| The questions are generated before the list of gaps is approved | Stop after the table of step 1 and wait |
| A spoken answer is treated as a requirement | It is an indication to validate, with channel and date |
| An answer is written straight into a chapter | Only `[PD]` then `[UK]` write into the vault |
| A skipped question is asked again | A skip is final |
| A question whose answer is in the code | Read the file instead of asking |

These restate rules of the original keeper (its workflows, its state rules and its vault conventions) as mistakes to avoid.

## Not yet proven

- The queued-question path (`di_candidate`) has been used rarely; the filter on `status` and `kind` is the part that was found necessary.
- The interview has not been run with the roles table of `_meta/roles-and-sources.md` filled in by a team.

## Validation checklist

Before declaring the `[DI]` closed:

- [ ] The rubric was applied to **all** of `{{VAULT_DIR}}`, not only to recent sections
- [ ] The 10 gaps do not all come from the same section (or it is justified)
- [ ] Every "why" is concrete, not "seems incomplete"
- [ ] Exactly 10 questions, one per gap, each with its destination chapter
- [ ] No question is answered by reading a file
- [ ] Both channels (chat or a file in `{{RAW_NOTES_DIR}}/`) and the option to skip were announced explicitly
- [ ] Every answer has channel, date and author role label
- [ ] Verbatim answers are **only** in `{{RAW_NOTES_DIR}}/`, zero lines written in `{{VAULT_DIR}}`
- [ ] Answers in conflict with the vault are listed as open items, not applied
- [ ] Skipped questions were not raised again
- [ ] `{{KIPI_STATE_DIR}}/state/<slug>.json` updated with the outcome and the raw file produced

Typical failures: generating the questions before the list of gaps is approved; treating a spoken answer as a requirement; writing into a chapter without going through `[UK]`.
