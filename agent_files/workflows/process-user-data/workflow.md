# Workflow `[UD]`: Process User Data (the raw-notes inbox)

> Modified on the branch `onegate` of `LairdLuca/kipi-agent`: the raw-notes pipeline with a ledger, an append-only archive and a source status by channel.

Goal: turn the raw inputs dropped into `{{RAW_NOTES_DIR}}/` into structured extractions ready to fold into chapters.

1. **Scan.** List the files in `{{RAW_NOTES_DIR}}/` (not recursive into `_archive/` and `ready-for-classify/`). Skip those already in `ledger.jsonl` (match on `path` and sha256 of the file).
2. **Classify.** Match the file name against `{{KIPI_HOME}}/data/knowledge-classification.csv`: target chapter, what to extract, and the **status the information carries by channel** (`conventions.md` section 1.1). A pattern that does not match is not guessed: ask. For each file propose target chapter, extraction kind, source status, confidence.
3. **Preview and wait.**
4. **Extract.** Write `{{RAW_NOTES_DIR}}/ready-for-classify/<original-name>.extracted.md` with frontmatter `source_file`, `source_sha256`, `target_chapter`. Low-confidence items go to `{{RAW_NOTES_DIR}}/ready-for-classify/_review/` with a note.
<!-- onegate-if: bmad -->
   For large or dense documents run `bmad-distillator` first.
<!-- onegate-endif -->
5. **Archive.** Move the original to `{{RAW_NOTES_DIR}}/_archive/<YYYY-MM>/`. Never delete from the archive.
6. **Ledger.** One line per file in `ledger.jsonl`, `record_type` set. If the `path` already has a line, **edit that line**: never add a second one.
7. **Report.** N processed, M flagged. Suggest `[UK]`.
