# Workflow `[CS]`: Code Scan & Document

> Modified in `LairdLuca/kipi-agent`: one chapter per run, capture by git branch, oversized sources declared.

Goal: fill one chapter, or one variant file, with deep content backed by source reads. Load `{{KIPI_HOME}}/workflows/capture.md`.

1. **Scope.** Which chapter to write.
<!-- onegate-if: multi-branch -->
   Also on which variant: if the chapter exists and the active variant differs from its `variant`, the output is a **variant file**, not an edit to the base.
<!-- onegate-endif -->
2. **Plan the read set.** List the source files before reading any. Wait for approval.
3. **Read.** Parallel `Read` calls, about 15 files at a time. A source on another branch is read with `git show <ref>:<path>`, without switching branch.
4. **Draft.** Frontmatter per `conventions.md` section 2. Body: TL;DR (at most 5 bullets), index, sections with `file:line` citations, code excerpts up to 30 lines, Mermaid when 3 or more entities relate.
<!-- onegate-if: multi-branch -->
   Base chapters of cross-cutting sections also carry a `## Variants` table.
<!-- onegate-endif -->
   Model the shape on a complete chapter of the same section.
5. **Preview.** Show the draft and flag every assumption. Wait for approval.
6. **Write and capture.** Persist, then capture on the commit read (`capture.md` section 2): branch, bare commit, `hash_method: git-blob`, one hash per source (and the `variant`, in a multi-branch project).
7. **Index and state.** Update the chapter row in `index.md` (base chapters only), `chapters.json` and the state file.

**One chapter per invocation** unless the user says otherwise.

**Oversized sources** (past roughly 1,500 lines, for instance a generated data-access file). Declare the strategy in step 2 and record it in the chapter:

- `signature_only`: public signatures and call sites, no bodies. The chapter must say so: it is a `[DI]` gap, not a completed area.
- `chunked_read`: slices of about 1,000 lines around the methods that matter, one slice per pass.
- `skip_with_note`: generated or vendored code; record that it exists and why it was not read.

Never truncate a read silently and write as if the file had been covered.
