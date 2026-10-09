# Workflow `[CL]`: Vault Audit and Maintenance

Origin: adapted from `kipi-agent` (ladymeyy, MIT), workflow `cleanup-knowledge`; modified: standalone, no BMad, audit against the conventions of this vault.
The structure contract that is verified is **not** upstream's (`core_knowledge/` + `private_knowledge/`, folder contracts, `_about.md`): it is `{{VAULT_DIR}}/_meta/conventions.md`, the constitution of this vault.

Goal: find the drift between what the vault declares itself to be and what it is. Three passes (Structure, Rules, Hygiene), then pointed proposals. **Kipi fixes nothing on its own**: every action needs an explicit OK.

This workflow never reads the code in `{{CODE_REPO}}`, except to recompute the hashes of sources already cited.

---

## Pass A: Structure (severity: critical)

Against `_meta/conventions.md` sections 2, 3, 5.

1. **Complete frontmatter.** Every `.md` in `{{VAULT_DIR}}` (excluding `index.md` and `_meta/sources.md`) must have `chapter_id`, `status`, `source_files`, `source_files_hashes`, `links_to`, `last_updated`; if it has sources in `{{CODE_REPO}}`, also `branch_at_capture`, `commit_at_capture` and `hash_method`.
<!-- onegate-if: multi-branch -->
   It must also have `variant` and `branches_covered`, except a `_meta/` file with `source_files: []`.
<!-- onegate-endif -->
   Report the missing fields file by file.
1b. **Capture format** (sections 2 and 11; mechanics in `workflows/capture.md`). Three defects, counted separately because they have different remedies:
   - **not captured**: a file with sources whose `hash_method` is missing or is not `git-blob` (a file with `source_files: []` has nothing to capture and is not counted). The file cannot be evaluated for staleness until it is recaptured;
<!-- onegate-if: multi-branch -->
     a missing `variant` counts the same, except in a `_meta/` file with `source_files: []`;
<!-- onegate-endif -->
   - **prose in the frontmatter**: `commit_at_capture` with text beyond the hash, `resync_note_*` fields or other notes. The history lives in `{{KIPI_STATE_DIR}}/uk-foldins.jsonl`;
   - **inconsistent capture**: a declared hash that does not match `git rev-parse <declared commit>:<path>`. Verify it on every file already in `git-blob` format: it is the check that says whether the capture is true.
<!-- onegate-if: multi-branch -->
1c. **Variant files** (section 3). For every file with `variant_of`: the base chapter exists; the name is `<chapter>.<slug>.md` with the slug of the variants table; `variant` is a single value and different from the base's; the `## Variants` table of the base links it. Conversely, every link to a variant file in a base table points to a file that exists.
1d. **Mixed chapters not yet split**: `branches_covered` with more than one variant and no variant file for those variants. It is not an error to fix in the audit: it is a count to report, with the chapters sorted by number of variants and of notes in the frontmatter, so that the next split starts from the worst.
<!-- onegate-endif -->
2. **`chapter_id` consistent** with the real path (relative path without `.md`). A moved and unrenamed chapter is silent drift.
3. **Allowed `status`**: only `placeholder` | `drafting` | `complete` | `stale`. A qualified form such as `complete (baseline view)` lives in `index.md`, **not** in the frontmatter: if it appears there, it is drift.
4. **`source_files_hashes` populated** when `status: complete`, empty when `placeholder`.
<!-- onegate-if: multi-branch -->
5. **`## Variants`** present in **every** cross-cutting chapter (the sections that `index.md` declares cross-cutting) and **absent** from per-variant chapters. See section 3.
6. **Unverified `(same as baseline)`**: a row of the Variants table that says `(same as baseline)` for a variant that does **not** appear in `branches_covered`. It is the most insidious violation of section 3: it affirms something unverified. Bring it back to `(not verified)`.
6b. **Consistency between the `_summary.md` and their chapters** (section 3.1). Section indexes carry a **derived** per-variant table: a deliberate duplication, so it must be checked, because no automatic mechanism can notice it: hashes watch the code, not the internal coherence of the vault.

   For every cross-cutting section compute the union of the `branches_covered` of its chapters and compare it with the variants the summary declares **covered** (the rows that do not say `(not verified)`). Two outcomes to report:

   - **The summary is behind**: a variant the chapters cover and the summary does not name. Annoying but harmless.
   - **The summary affirms more than the vault knows**: a variant declared covered with no chapter reporting it. This is the serious case, because the derived view is inventing coverage.

   Three sources of **false positives** to exclude before reporting:

   - **Coverage outside the section.** When the divergence of a variant is structural, section 3 asks for a dedicated chapter elsewhere: the summary may legitimately cite it. It is recognisable because the row points to a chapter of another section. Not a violation, the convention applied.
   - **Different spellings of the same variant.** If the `branches_covered` do not use a single form per variant, the comparison produces differences that do not exist. Normalise first, and **report the inconsistent spelling as an item of its own**: it is a real defect that makes every count per variant unreliable.
   - **Citation as a hypothesis.** A chapter may name a variant while saying explicitly that it is probable or to be confirmed. That **is not** coverage and must not be in `branches_covered`; if the summary lists it, mark it `(not verified)` with the hypothesis described next to it.

   The inverse case is a defect of the **chapter**, not of the summary, and is reported there: a chapter whose body verifies a variant while its `branches_covered` does not list it. The frontmatter lags behind what the chapter has already established, and every count per variant understates it.

   When they disagree the source is always the chapter.
<!-- onegate-endif -->
7. **Broken links** (section 5): every `links_to` that points to a nonexistent file. **Asymmetries are not checked**: return links are optional, and section 5 asks not to reopen them.
8. **Alignment with `index.md`**: base chapters on disk missing from the index (orphans), index rows that point to nonexistent files (broken links), coverage counters that disagree with the real count of files.
<!-- onegate-if: multi-branch -->
   A variant file is not an orphan if its base chapter links it: the index lists only base chapters.
<!-- onegate-endif -->

---

## Pass B: Mandatory rules (severity: critical)

Against the project rules file (`CLAUDE.md` of the workspace, when present) and `_meta/conventions.md` sections 1, 4, 9, 14.

1. **Sensitive data** (section 4, the mandatory rule of the project). Scan every file for: IP addresses, `Password=`, `User Id=` or `Data Source=` with a real value, API keys and tokens, license keys, real customer or user codes, personal identifiers, names of servers and database instances. Every occurrence with `file:line`. **This is the pass that is never skipped.**
2. **Verifiable citations** (section 1): technical statements with `file:line` whose file no longer exists on the branch declared in `branch_at_capture`. Not necessarily an error (the branch may have changed), but report it.
<!-- onegate-if: multi-branch -->
3. **Branch prefix** (section 1): the citation of a file that is not on the baseline without the `<BranchName>:` prefix, in a file whose `variant` differs from the cited branch's.
<!-- onegate-endif -->
4. **Reserved Windows file names** (section 9): no file named `nul`, `con`, `prn`, `aux`, `com1` to `com9`, `lpt1` to `lpt9` inside the vault or in `{{RAW_NOTES_DIR}}`.
5. **Vault/memory duplication** (section 14): if memories visible in the session contain `file:line`, commit hashes or the state of a task, that detail has one home and it is not the memory. Report it as a proposal to move.

---

## Pass C: Hygiene (severity: warning)

1. **Oversized chapters** (section 10): over 600 lines or over 15 subsections: propose the split, saying which sub-area is cohesive enough to become a chapter of its own.
2. **Code excerpts over 30 lines** (section 6) and code blocks with no language.
3. **Missing Mermaid** (section 7): text descriptions that relate 3 or more entities with no diagram. An informational report, not an action.
4. **Communication-log lines pending**: lines with `**State:** awaiting answer` older than 7 days. List them with date and recipient role: they are the starting material for a `[DI]`.
4b. **Names and role labels** (detector for `_meta/roles-and-sources.md`, when the vault has it). That chapter has `source_files: []`: the hash check never touches it, so its drift is measured another way. Collect the proper names that appear **outside** that file (the rule is that names live in one place) and the role labels used in logs and chapters, compare them with its table, and report:
   - proper names found in a chapter or a log (to replace with a role label);
   - role labels used in chapters that **are not in the table** (their weight under section 1.1 is undeterminable today);
   - `(not declared)` entries still open, with how long they have been open, so they become questions for the next `[DI]`;
   - people in the table who no longer appear in any chapter for over six months (informative, not an action: people change projects).
   Never deduce a role from occurrences: the detector produces **questions**, not table rows.
5. **Raw-notes pipeline**: files in `{{RAW_NOTES_DIR}}/ready-for-classify/` never folded into a chapter; files in `{{RAW_NOTES_DIR}}/` never processed and absent from `{{KIPI_STATE_DIR}}/ledger.jsonl`; integrity of `{{RAW_NOTES_DIR}}/_archive/` (append-only: no deletions, structure `YYYY-MM/`).
6. **Real staleness** (section 11): evaluate every file as described below. A comparison made by recomputing the hash of the file on disk, without looking at which branch the capture belongs to, produces almost only false positives.
7. **Index drift**: the `Last [CD]/[UK]/[CL]` line at the top of `index.md` older than the latest `last_updated` among the chapters.

### How to read a diverging hash (mandatory before declaring stale)

A hash answers one question only: **"has the file I documented changed since then?"** The mechanism presupposes that today's file and the file of then are the same file.
<!-- onegate-if: multi-branch -->
In a repository with **one branch family per variant** that presupposition fails: the same path is a different file on every branch, and comparing the hash taken on one branch with the file of another measures the distance between the two branches, not the passing of time.

For this reason every vault file declares its own `variant` and its hashes are git blobs of a precise commit: they are compared with the same path **on the reference branch of that variant**, read by ref, without switching branch. Procedure and allowed outcomes: `workflows/capture.md` section 3. Load it before this pass.
<!-- onegate-endif -->
<!-- onegate-if: single-branch -->
Hashes are git blobs of a precise commit: they are compared with the same path on the two references of `workflows/capture.md` section 3 (the default branch and the tip of the capture branch), read by ref, without switching branch. Load that file before this pass.
<!-- onegate-endif -->

Rules of the audit itself:

- **Declare the reference.** For each reference evaluated, which git ref you used and at which commit and date it points. A stale local ref makes look current what is not.
- **`not captured` and `reference missing` do not enter the count of stale files.** Report them separately. Adding them to the others inflates the number and hides the real diagnoses.
- **The audit does not recapture.** Writing new hashes is the work of `[UK]` after re-reading the sources. A hash updated without re-reading the code declares verified a text that nobody rechecked.
- **Sources outside the code repository** (project rules, environment notes) have no branch: they are always evaluable, and a difference is real staleness.
- **Do not switch branch in `{{CODE_REPO}}`.**

---

## Presentation and application

Group by pass and severity, then list the **numbered** actions, each with the file and line that motivates it:

```
🔍 Vault audit - <date>

A. Structure (critical)      N items
B. Mandatory rules (critical)  N items
C. Hygiene (warning)         N items
D. Healthy                   N items (one line only)

Proposed actions (N):
[1] <action with file:line>
...
[A] approve all the critical   [H] hygiene only   [N] report only
```

Stop and wait. Then, for every approved action: say what will change, apply it, re-verify the rule that triggered it, report the outcome.

Constraints when applying:

- Sensitive data are replaced with the placeholders of the section 4 table; never delete the line without saying so.
<!-- onegate-if: multi-branch -->
- `(same as baseline)` becoming `(not verified)` is a correction, not a loss: the variant had not been verified.
<!-- onegate-endif -->
- A chapter is not promoted to `stale` without an actual hash recomputation.
- Nothing is ever deleted from `{{RAW_NOTES_DIR}}/_archive/` (escalation rule of the persona).
- If the audit touches `index.md`, the session summary at the top is **not rewritten**: it is appended to.

Save the report to `{{KIPI_STATE_DIR}}/reports/audit-vault-<YYYY-MM-DD>.md` and update `{{KIPI_STATE_DIR}}/vault.json` (audit outcome) and the state file of the branch. If the audit found **duplicate keys** in the `.jsonl` files, deduplication is proposed as an action, not applied by default: two records under the same key may be the same fact written twice or two different facts to tell apart with a field, and the difference is not decided alone.

---

## Common mistakes

| What happens | Remedy |
|---|---|
| The audit stops after pass A because it already found a lot | Run all three passes, always; pass B is never skipped |
| Stale counted from the hash of the file on disk | Evaluate by git ref, as in `capture.md` section 3 |
| `not captured` files added to the stale count | Report them as a separate item |
| A fix applied because it looked obvious | No fix without an explicit OK, item by item |
| Return-link asymmetries reported as defects | They are optional by convention; do not report them |

These restate rules of the original keeper (its workflows, its state rules and its vault conventions) as mistakes to avoid.

## Not yet proven

- The detector 4b (names and role labels) is new writing for the generic vault: it has not run on a real vault.
- The single-branch variant of this audit has not run on a real project.

## Validation checklist

- [ ] All three passes were run, even if the first already found a lot
- [ ] The sensitive-data scan covered **all** the files, not a sample
- [ ] Every item has `file:line`, not a generic description
- [ ] Staleness was evaluated **by git ref**, not deduced from `last_updated` or from the hash of the file on disk
- [ ] For every reference evaluated the ref is declared, with commit and date
- [ ] `not captured` and `reference missing` are reported apart instead of entering the stale count
- [ ] No hash was rewritten by the audit
- [ ] No correction applied without an explicit OK, item by item
- [ ] Link asymmetries were neither reported nor "fixed"
- [ ] `index.md` reflects the real structure after the applied actions
- [ ] Report saved and `{{KIPI_STATE_DIR}}` updated (only the files touched)
- [ ] Duplicate keys searched in the `.jsonl` files (`path` for the ledger, `id` for the others): with in-place rewritable files a double key is a defect, not a legitimate append
- [ ] Items not approved stay in the report as notes, they do not vanish
