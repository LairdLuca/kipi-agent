# Capture: hash, stale and variant files

Mechanics shared by `[CS]`, `[UK]`, `[CL]` and `[ST]`. The **contract** (what a vault file must declare) lives in `{{VAULT_DIR}}/_meta/conventions.md`, sections 2, 3 and 11; this file holds only **how to do it**. If the two diverge, `conventions.md` wins and the divergence is reported.

Principle: **one file, one capture.** Every vault file is captured on one branch and its hashes are all taken at the same commit. The same path can be a different file on every branch family, so a hash only means something together with the branch it was taken on.

---

<!-- onegate-if: multi-branch -->
## 1. Variant

The variant of a branch is given by this rule: {{VARIANT_RULE}}.

```bash
git -C "{{CODE_REPO}}" branch --show-current     # community/1234-payment-retry  ->  community
```

The canonical spelling of each variant, its slug and its reference branch are read from the table of known variants in `{{VAULT_DIR}}/_meta/variants.md`. The slug is the same as the one of the state file `{{KIPI_STATE_DIR}}/state/<slug>.json`. The baseline variant is `{{BASELINE_VARIANT}}`. The reference branch of a variant follows this pattern, with the variant's prefix in place of `<variant>`: `{{VARIANT_REFERENCE}}`.

A prefix that is not in the table (a topic branch, a client never documented) **is not guessed**: ask which variant it belongs to, or state that the branch is not covered by the vault.
<!-- onegate-endif -->
<!-- onegate-if: single-branch -->
## 1. Branch

This project has one branch family: there are no variants and no variant files. The branch you read is recorded in `branch_at_capture`. The state file is `{{KIPI_STATE_DIR}}/state/default.json`.

The reference branch is the default branch of the code repository:

```bash
git -C "{{CODE_REPO}}" symbolic-ref --short refs/remotes/origin/HEAD     # origin/main
```

If that command fails (no remote, no `origin/HEAD`), ask the human which branch is the reference and note the answer in `{{KIPI_STATE_DIR}}/note.md`.
<!-- onegate-endif -->

## 2. Capture

Capture **on a commit**, never on the working tree:

```bash
git -C "{{CODE_REPO}}" rev-parse --short=9 HEAD                       # capture commit
git -C "{{CODE_REPO}}" rev-parse <commit>:<path> | cut -c1-8          # hash of one source
```

The paths of `source_files` are written from the root of the code repository, without the repository's folder name.

In the frontmatter: `branch_at_capture` (the git branch read), `commit_at_capture` (**bare hash**, no prose next to it), `hash_method: git-blob`, and one hash for each entry of `source_files`.
<!-- onegate-if: multi-branch -->
Add `variant`, the variant of the branch read.
<!-- onegate-endif -->

- **Source on another branch**: there is no need to switch branch. `git -C "{{CODE_REPO}}" show <ref>:<path>` reads it and `rev-parse <ref>:<path>` gives its hash. Declare the commit `<ref>` points to, not the branch name.
- **Source with uncommitted changes**: the capture stays on the last commit. If the documented fact comes from the uncommitted change, the text of the chapter says so, with the date.
- **Source outside the code repository** (a workspace file such as the project rules file): `git hash-object <path> | cut -c1-8`. It has no branch: it is always evaluable. If the vault file cites a **past version** of that source, the hash is the blob of that version (`git rev-parse <workspace commit>:<path>` in the repository that holds it), the workspace commit is declared in the bridge box, and the file turns out stale against today: that is correct.
<!-- onegate-if: multi-branch -->
- **File with `variant: none`** (no source in the code repository): the fields `branch_at_capture` and `commit_at_capture` are not written.
<!-- onegate-endif -->
<!-- onegate-if: single-branch -->
- **File with no source in the code repository**: the fields `branch_at_capture` and `commit_at_capture` are not written.
<!-- onegate-endif -->
- **Folder source**, for chapters that are an inventory by name (the procedures of a schema, the packages of a project): the entry of `source_files` is the folder, written with the trailing `/`, and its hash is the **git tree id** at that commit, `git -C "{{CODE_REPO}}" rev-parse <commit>:<folder> | cut -c1-8`. It changes exactly when a file of the folder changes, is added or removed, so it watches the list. Use it only for folders whose content the text states as a list or a count, not in place of files cited with a line. A count of zero because the folder does not exist is a statement of absence: there is no tree to hash and it stays unwatched, declared in the bridge box.
- **File excluded from git** (a local editor settings file, a downloaded package folder): it has no blob and cannot be a source. The text says it is an observation on a local copy, with the date; it does not enter `source_files`.
- **A hash that is not the blob of the declared commit is never written.** Before closing a capture, recompute one hash as a sample and compare it.

## 3. Evaluate staleness

For every vault file with `hash_method: git-blob`:

<!-- onegate-if: multi-branch -->
1. There are two references, and **neither depends on the checked-out branch**: `D`, the reference branch of the file's variant (`_meta/variants.md`), and `T`, the tip of the branch declared in `branch_at_capture`, if that branch still exists (`origin/<branch>`, otherwise the local branch).
<!-- onegate-endif -->
<!-- onegate-if: single-branch -->
1. There are two references, and **neither depends on the checked-out branch**: `D`, the default branch of the code repository (section 1), and `T`, the tip of the branch declared in `branch_at_capture`, if that branch still exists (`origin/<branch>`, otherwise the local branch).
<!-- onegate-endif -->
2. For every source: `git -C "{{CODE_REPO}}" rev-parse <reference>:<path> | cut -c1-8` and compare with the declared hash, first on `D` and then on `T`.
3. The file is **current** if all hashes match on `D`, or all on `T`. It is **stale** if it diverges from both, or from `D` when `T` no longer exists. What changed: `git -C "{{CODE_REPO}}" diff <capture commit> <reference> -- <path>`.

The second reference is for files captured on the branch of a task not yet merged: they describe real code that the reference branch does not hold yet. Evaluating them on `D` alone would make them stale by geography; evaluating them on `HEAD` would change the outcome at every checkout change, which happened (on acme-shop, several chapters captured on an unmerged task branch turned stale when another session moved the checkout to the other edition).

Possible outcomes, and only these:

| Outcome | When |
|---|---|
| `current` | all hashes match on `D` |
| `current on task branch` | they diverge on `D` but all match on `T`: the task is not merged yet. Say it so, with the branch name: when the task enters the reference branch the file must be re-evaluated |
| `stale` | at least one hash diverges on both; report the paths and the `git diff --stat` |
| `source missing` | the path does not exist on the reference: renamed, moved or deleted. It is stale, with this cause. At recapture the path is updated to the new one |
| `not captured` | the file has sources and no `hash_method: git-blob`: it cannot be evaluated until it is recaptured. It is neither stale nor current |
| `reference missing` | `D` does not exist locally. Declare which one and stop there for that file |

To know whether a working branch has been merged into the reference branch, `merge-base --is-ancestor` is not enough when pull requests enter as squash: the branch commits do not become ancestors. Count the content: if the sources have on the reference branch the same blobs as the tip of the branch, it has been merged.

Always declare **on which reference and at which commit** you evaluated: a local reference branch can be weeks old. `git fetch` in the code repository is allowed (it does not touch the working tree), **unless the mandate forbids every write in `{{CODE_REPO}}`**: then do not. Without a fetch, or if it fails, report the date of the commit each reference points to.

Do not switch branch in `{{CODE_REPO}}` to evaluate or capture: it is not needed, and it breaks the work of whoever has the project open.

## 4. Recapture after an update

When `[UK]` updates a stale file: read the diff between the old capture commit and the new one, update the text, then move `commit_at_capture` to the new commit and **recompute all the hashes of the file at that commit**, not only the one that triggered the stale. A file with hashes taken at two commits is a mixed capture again.

**One more source without recapturing.** If a file starts to cite a source it did not have, add it to `source_files` with the blob **at the capture commit already declared**: all hashes stay at one commit and the capture does not move.

**A fact true at the capture commit but already changed on the reference.** In a stale file, a fact verified at the capture commit is written **with that commit next to it**, not in the present tense; what changed on the reference is noted as an open point in the branch's state file, for the round that will recapture.

**`status` follows the evaluation.** After every capture or recapture evaluate the file (section 3): if it is stale, `status: stale`, otherwise `complete`. The same value goes in the row of `index.md` (base chapters only) and in `chapters.json`.

If you update the text **without** having re-read the sources (a fact that came from a test round or a call), the capture is not touched.

Other cases of recapture:

- **A non-stale file whose sources you re-read on the reference** (to correct a finding) can be recaptured there: the hashes do not change, only the declared commit does, which then matches the one at which lists and lines were counted.
- **A source with several identical copies in the repository** whose existence the text affirms: every copy is a source.
- **A finding left by an earlier round** ("seen, not corrected") is verified before correcting it: it can be wrong itself.
- **`last_updated`** is the day the file was really modified, even if the round started the day before.

<!-- onegate-if: multi-branch -->
## 5. Where a new fact goes

| The fact concerns | Where it is written |
|---|---|
| the variant of the base chapter | in the base chapter |
| another variant, and its variant file exists | in the variant file |
| another variant, and its variant file does not exist | create the variant file (section 6) and add the row to the `## Variants` table of the base |
| a comparison across variants ("on none of the three...") written now | the summary in the variants table of the base; the detail with sources in each variant file |
| a comparison across variants **already written** as a single section (a table that sets the variants side by side) | stays where it is, in the base, with every citation labelled with its variant and commit: separating it would mean rewriting it, and a split does not rewrite (section 7) |

**Never** write into one file a fact read on a different variant and leave a note "hashes not touched": it is the defect that variant files exist to remove.

## 6. Create a variant file

Name: `<chapter>.<slug>.md`, next to the base chapter.

```yaml
---
chapter_id: checkout/payment-retry.enterprise
variant_of: checkout/payment-retry
status: complete
variant: enterprise
branch_at_capture: enterprise/1234-payment-retry
commit_at_capture: <hash>
hash_method: git-blob
source_files:
  - src/checkout/payment-adapter.js
source_files_hashes:
  src/checkout/payment-adapter.js: "<blob8>"
branches_covered: [enterprise]
links_to:
  - ./payment-retry.md
last_updated: <today>
---
```

Body: an opening bridge box that points back to the base chapter, says in what this variant diverges (or that the section was read on this variant but not compared with the others) and at which commit the cited lines are; then only what is proper to the variant. No repetition of the common story. A citation of a branch different from the file's one stays labelled with its branch (`conventions.md` section 1).

The sources of a file are those the text cites with a line and those whose content it affirms (a value read in a resource file, the shape of a class). A class or method merely named is not a source; it becomes one if the text describes how it works and the file holding it is only one, even when the text does not write the path. A table or a value named without a file, but whose schema or content the text affirms, has for source the file that defines it on that variant, if it is only one; if there are several or it is not known which was read, it stays out and the report says so. If a row of the variants table of the base affirms the content of a file of another variant, that file is a source of that variant's file, when it exists, and its bridge box says so. **A statement of absence is content asserted**: "the solution does not list these projects" makes the solution file a source, "these folders are empty" makes the folders sources. It costs one more stale when the file changes a lot, but without the source the chapter stays `current` while saying something false: on acme-shop, a chapter declared the plugins folder of the enterprise edition empty; plugins later arrived there from the baseline, and the chapter stayed current because the folder was not a source. An environment finding with no code (a test round, a measurement) goes in the file of the variant on which it was observed and adds no sources: a schema **measured on a database** does not make the file that defines the table a source, a schema **read in the file** does. An example that the text declares illustrative does not affirm the schema of the tables it uses and adds no sources.

In the base chapter, the row of the variant in `## Variants` carries a one-sentence summary and the link to the file. The `branches_covered` of the base lists every verified variant of its table, that is, those the chapter and its variant files cover together (a row is verified if it reports code read or a measurement; a row is not verified if it carries `(not verified)` or is written as a hypothesis, "probably", "check whether". The variants of examples read inside a rule valid for all variants, and of notes read on a variant and left in the base, also count); the one of a variant file lists only its own.

A variant file is **not an extra chapter** for the counts: it does not enter `index.md` nor the coverage counters of `vault.json`. It does enter `chapters.json`, with a line of its own carrying `variant_of`.

## 7. Split a mixed chapter

A chapter is mixed when `branches_covered` has more than one variant and the text holds sections read on different variants, or when its hashes are not all of the same commit. Splitting is a structural operation: it is done only on a mandate that names it (see the Workflow part of `kipi-update-core`, "What is never applied unasked").

1. **Inventory.** For every section, on which variant and at which commit it was read. The text says it ("variant enterprise @ `<hash>`"), the notes in the frontmatter, if any, and `uk-foldins.jsonl`. A section whose variant cannot be established **stays in the base** and is reported: it is not attributed to a variant by plausibility. If establishing it would need re-reading a lot of code, the chapter is not split in this round: it is split whole or not touched.
2. **Move, do not rewrite.** The sections of a variant pass verbatim into the variant file. A section runs from its heading to the next heading of the same or higher level, subheadings included; a line of mixed provenance inside it follows the section and is reported, not split. A subsection **with a heading of its own** read on another variant inside a base section is a section by itself: it goes to that variant's file and its heading stays in the base as an anchor. The exception is a subsection that describes only files **identical at the base's capture commit**: it stays in the base, because it describes its code too, and its files enter the base's sources. A note without a heading read on another variant (a paragraph, a one-line box) inside a base section stays where it is, and the bridge box declares its variant, commit and date. A rule valid for all variants stays in the base even if it was born on one variant and carries that variant's examples: the bridge box declares the commit the examples come from. For the same reason, if a section to move holds a subsection with its own heading that holds for all variants, or a block that belongs to the base's variant, cut at that heading: that part stays in the base under the heading left as an anchor, and both bridge boxes say so. Only internal links are adjusted. Rephrasing during a move makes the diff unreadable and hides losses. This rule prevails over section 5. To move many lines a script that cuts by line number is convenient, **after checking that the boundary lines are the expected ones**; keep a copy of the original in the scratch folder for the verification of point 7.
3. **Anchors.** Other chapters may link to a moved section: search the file name in `{{VAULT_DIR}}` before moving, and leave in the base the heading of the section with a pointer line to the variant file, so anchors and cross-references keep leading somewhere.
4. **Capture of each file.** The capture commit is the one **to which the cited lines return**, not the one the frontmatter declared (if the file cites no lines, it is the tip the declared branch had when the text was written: the date of the workspace commit that added the chapter, then `git -C "{{CODE_REPO}}" log -1 --until=<date> <branch>`; the claims of the text are checked at that commit): open it with `git show <commit>:<path>` and check a few `file:line` of the main section. The two may differ (hashes taken on the working tree of another task). If a file gathers sections of the same variant read at different commits, the capture is the commit of the main section and the others declare their own commit in the bridge box: their sources have no hash, and the report says so. A source that serves only one of those sections **leaves `source_files`** even if it was there, and stays named in the bridge box with its commit: it comes back with a hash at the next recapture. The exception is a source whose cited lines are **identical at the capture commit**: it enters `source_files` with the capture's blob, because the text is true there as well. The exception holds even when the part was read on another variant: what counts is that the content matches, not where it was read nor whether the sentence citing it is right (a wrong sentence is reported). If one section alone cites lines of two commits, the capture is the commit at which its main content is true, and the box declares the other. A base chapter can have a bridge box too, when it gathers several commits. Then recompute the hashes at that commit with `rev-parse`. If the commit is no longer reachable (`git cat-file -t <commit>` fails), capture on today's reference branch **only after re-reading the sources**; otherwise leave `status: stale` and say so.
5. **Base chapter.** Add `variant`, set `hash_method` to `git-blob` recomputing the hashes at the capture commit, remove the prose from `commit_at_capture`, update the variants table, then evaluate and set `status` (section 4).
6. **Frontmatter notes.** Any note in prose and any key outside the schema of `conventions.md` section 2 (also dates and service fields such as `last_resynced_at`) leave the frontmatter and enter verbatim, in date order, in a final section `## Update history` of the file they refer to. A note about two files goes whole in the one that holds the section the note is about, with a pointer line in the history of the other. A `links_to` that served only a moved section passes to the variant file and leaves the base. A key that holds several updates of different dates is split at their boundaries only when the pieces go to different files, each piece verbatim in its file.
7. **Verification.** No line of the original chapter has disappeared. Compare the body lines **as a multiset**, not only the total: every original line must be found in one of the resulting files, and the difference is explained in three items (adjusted lines, new bridge and history lines, headings left in the base as anchors and therefore present twice). Every hash written matches `rev-parse` at the declared commit: recompute them all after writing. No relative link is broken in the resulting files.
<!-- onegate-endif -->

---

## Common mistakes

| What happens | Remedy |
|---|---|
| A hash is taken on the working tree, or at two different commits | Capture on one commit; recompute every hash of the file at that commit |
| Staleness is evaluated on `HEAD` and changes with every checkout | Evaluate on the two references of section 3, never on the checkout |
| Text is edited without re-reading the sources and the capture is moved anyway | A capture moves only after the sources were re-read on the reference |
| `(same as baseline)` is written for a branch nobody read | Write `(not verified)` and list in `branches_covered` only the branches read |
| A path stayed in `source_files` after the file was renamed on the reference | The outcome is `source missing`; fix the path at recapture |
| The branch is switched in `{{CODE_REPO}}` to read another variant | Read it by ref with `git show` and declare the commit |

These restate rules of the original keeper (its workflows, its state rules and its vault conventions) as mistakes to avoid.

## Not yet proven

- Only the multi-branch mechanics ran on a real project. The single-branch path (one reference, no variant file) is new writing.
- Folder sources (tree ids) were used on few chapters.
