# === Kipi: Project Rules (managed) — start ===
# Managed by Kipi (`/kipi-install`). Edit by re-running install or by toggling the
# block manually between the start/end markers. Content outside the markers is
# preserved on every Kipi run.
# Modified on the branch `onegate` of `LairdLuca/kipi-agent`: the vault of this branch, a closed write list, the raw-notes pipeline.

## Knowledge Verification Protocol (CRITICAL)

**Core-Knowledge is a STARTING POINT, not an AUTHORITATIVE SOURCE.**

When using knowledge files:
1. **Treat as orientation** — Use knowledge docs to understand codebase structure, key concepts, and where to look.
2. **Always verify specifics** — Before making claims or changes based on knowledge docs, verify details in the actual code.
3. **Knowledge may be stale** — Code evolves faster than documentation; trust the code when they conflict.
4. **Ask when uncertain** — If knowledge docs don't provide enough detail, ASK the user or INVESTIGATE the codebase.
5. **Don't guess** — Never fill in gaps by assuming; verify or ask.

### Verification Workflow

```
Knowledge Doc → Understand Structure → Find Relevant Code → Verify Details → Act
```

---

## Base Knowledge Protocol

Before responding to any user request:

1. **Check for the vault:**
   - Look for `{{VAULT_DIR}}/index.md`.
   - If it exists, scan the index (the routing table) to understand the available knowledge.
   - Load only the relevant knowledge files based on the user's request.
   - **Use knowledge as a MAP to navigate to actual code.**

2. **Context Loading:**
   - DO NOT load all knowledge files — only what's relevant.
   - If unsure which knowledge applies, ask the user.
   - Reference knowledge naturally in responses when applicable.
   - **Always verify specifics in code before acting.**

---

## Kipi Handshake Protocol

**CRITICAL:** This rule applies to ALL agents in this project — **EXCEPT Kipi itself**.

### When NOT to Trigger

Skip the handshake entirely if:
- The current agent IS Kipi (Core-Knowledge Keeper).
- You are executing a Kipi skill/workflow (e.g. `/kipi-update-core`).
- The user is already in a Core-Knowledge update session.

Kipi manages Core-Knowledge directly — it doesn't need to suggest calling itself.

### When to Trigger

After completing any of these actions (and you are NOT Kipi):
- Making changes to source code files.
- Creating new files in the repository.
- Receiving new project-level information from the user.
- Completing a significant task that affects project structure.

### What to Do

Append this message at the end of your response:

> **Knowledge Update Available**
> Project knowledge may need updating based on these changes.
> → Run `/kipi-update-core` to sync Core-Knowledge with Kipi.

### Why This Matters

- Keeps Core-Knowledge synchronized with actual code.
- Ensures documentation doesn't drift from implementation.
- Maintains a single source of truth for project knowledge.
- Enables developers to always have accurate context.

### Post-development hook

At the end of a task that changed code in `{{CODE_REPO}}`, propose `kipi-update-core` to bring the vault up to date.

---

## Kipi Skill Reference

Kipi's brain is at `{{KIPI_HOME}}`. `{{KIPI_HOME}}/SKILL.md` is the entry; in an editor with no skill or command folder, load `{{KIPI_HOME}}/<skill>/SKILL.md` and run it inline.

### `/kipi-update-core` (`[UK]`)
**Purpose:** Fold what changed into the vault.
**When:** after code changes, after receiving new project info, when the vault may be outdated, before a `/clear` or the closing of a session that produced knowledge.
**What it does:**
1. Collects the signals: the session, the ready extractions, the code drift of the chapters in play.
2. Applies targeted edits to the chapters, each line with its source and its status, then reports what changed. The review happens on the git diff.
3. Records the fold-in in the state folder. It does not commit.

### `/kipi-developer-interview` (`[DI]`)
**Purpose:** Run 10 targeted questions to fill the gaps the vault declares.
**When:** after the first chapters exist but content is sparse, after onboarding, periodically.
**What it does:** ranks the gaps from the vault's own signals, asks one question each, collects the answers in the raw-notes inbox as indications to validate. Nothing is written into the vault from the interview: the answers re-enter through `[UD]` then `[UK]`.

### `/kipi-cleanup-knowledge` (`[CL]`)
**Purpose:** Audit the vault against its own writing conventions.
**When:** after major changes, periodically.
**What it does:** three passes (structure, rules, hygiene), numbered proposals, and only what the person approves one by one is applied. The sensitive-data pass is never skipped.

### `/kipi-install` (`[IN]`)
**Purpose:** Install or repair Kipi for the editors in use.
**When:** at the first start (Kipi shows the plan by itself), after copying the brain again, when adding an editor, when a stub, a command or this block is missing.
**What it does:** shows what it will write, asks, then copies the stubs and the commands, writes this block between its markers and records what it wrote. Idempotent.

The other capabilities have no command file: `[RI]` (copy the vault skeleton into an empty vault), `[CS]` (code scan), `[UD]` (raw notes) and `[ST]` (status) are picked from the `/kipi` menu (where the editor has skill stubs, they can also be called by name).

---

## Folder Awareness

### Key Directories

| Folder | Purpose | Kipi Interaction |
|--------|---------|------------------|
| `{{VAULT_DIR}}/` | The knowledge vault: `index.md` (the router), `_meta/conventions.md` (the writing rules), the project's own chapters | Kipi writes here, only through its workflows |
| `{{RAW_NOTES_DIR}}/` | The raw-notes inbox: pasted messages, call notes, documents | Anyone drops files here; Kipi classifies them |
| `{{RAW_NOTES_DIR}}/ready-for-classify/` | Extractions waiting to be folded into a chapter | Kipi writes here |
| `{{RAW_NOTES_DIR}}/_archive/` | The originals, by month, append-only | Kipi archives here and never deletes |
| `{{KIPI_STATE_DIR}}/` | Kipi's state: per-branch state, chapters, ledger, fold-ins, todo, reports | Kipi writes here |

Kipi 🧠 keeps `{{VAULT_DIR}}`. **Knowledge enters the vault ONLY through Kipi's workflows** (`[RI]` skeleton, `[CS]` code scan, `[UD]` raw notes, `[UK]` update, `[DI]` developer interview, `[CL]` audit): no direct write into the vault outside them. The writing conventions of the vault are in `{{VAULT_DIR}}/_meta/conventions.md`. `[DI]` collects answers in the raw-notes inbox and they re-enter through `[UD]`; `[CL]` proposes and never fixes on its own.

### Where Kipi may write (closed list)

`{{VAULT_DIR}}/`, `{{RAW_NOTES_DIR}}/ready-for-classify/`, `{{RAW_NOTES_DIR}}/_archive/` (append-only, never delete), the interview answers file in `{{RAW_NOTES_DIR}}/`, and `{{KIPI_STATE_DIR}}/`. Outside them Kipi does not write: if the code must change, the request goes to the right agent. The only other writes are the installer's, after the person's yes: the editor's skill and command folders, this block between its markers, the record of the install, and the deletion of the install line of `{{KIPI_HOME}}/SKILL.md`.

**Raw-notes pipeline:** raw files in `{{RAW_NOTES_DIR}}/` are distilled into `ready-for-classify/*.extracted.md` (frontmatter `source_file`, `source_sha256`, `target_chapter`), the originals move to `_archive/<YYYY-MM>/` (append-only), and the fold-in into the vault goes through `[UK]`.

### Hard Rules (enforced across all writing skills)

1. **Names of people** appear only in `{{VAULT_DIR}}/_meta/roles-and-sources.md`, when the vault has one. Everywhere else, refer to people by role label.
2. **Sensitive data never enters the vault**, in any form: no secret, no real value, no connection string with credentials. Placeholders only (`{{VAULT_DIR}}/_meta/conventions.md`, section 4).

---

## Integration with Other Agents

Any agent in this project should:

1. **On Activation:** Check `{{VAULT_DIR}}/index.md` for relevant context.
2. **During Work:** Reference the vault as a STARTING POINT, then verify specifics in actual code.
3. **After Changes:** Trigger the Kipi Handshake (suggest `/kipi-update-core`), **unless you ARE Kipi**.
4. **Always Remember:** Knowledge docs are a MAP, not the territory: code is the source of truth.

**Note:** Kipi is exempt from the handshake protocol since it manages the vault directly.

# === Kipi: Project Rules (managed) — end ===
