# Workflow `[RI]`: Repository Initialize

> Modified on the branch `onegate` of `LairdLuca/kipi-agent`: copies the vault skeleton and creates the folders of first use; there is no scan and no proposal of a structure.

Goal: give an empty vault the two files the other capabilities rely on, a router and a contract. The sections of the vault are not decided here: they are the project's own and are added by `[CS]`.

**Workflow ID:** `repository-initialize`
**Menu Trigger:** `[RI]`

---

1. **Check the vault.** List `{{VAULT_DIR}}`. If it holds `index.md` and `_meta/conventions.md` already, the vault is initialized: say so, suggest `[CS]`, and stop. If it holds other files but not both, **stop and say so**: `[RI]` never runs on a vault that has content, and never overwrites.
2. **Say what will be written and wait for the OK:**
   - `{{VAULT_DIR}}/index.md`, from `{{KIPI_HOME}}/templates/index-template.md`
   - `{{VAULT_DIR}}/_meta/conventions.md`, from `{{KIPI_HOME}}/templates/conventions-template.md`
   - the raw-notes folders when missing: `{{RAW_NOTES_DIR}}/`, `{{RAW_NOTES_DIR}}/ready-for-classify/`, `{{RAW_NOTES_DIR}}/_archive/`
   - the state files of first use, only the missing ones (the table "First use" of `{{KIPI_HOME}}/SKILL.md`)
3. **Copy.** Copy the two templates as they are. Never rebuild them from memory. If a placeholder (a name written between double braces) is left in a copied file, stop and ask: the install did not fill it.
4. **Create the folders and the state files** that are missing. Never overwrite an existing file.
5. **Report.** One screen: what was created and what already existed. Then suggest `[CS]` on the area that is needed first, and `[UD]` if the raw-notes inbox already holds files.

`[RI]` does not commit.

## Common mistakes

| What happens | Remedy |
|---|---|
| The skeleton is rebuilt from memory because a template is missing | Stop: a missing file of the brain is repaired by copying `agent_files/` again, not by writing from memory |
| `[RI]` runs on a vault that has chapters | It never does: say that the vault is initialized, or stop and ask |
| The state folder is recreated and a line of another session is lost | Create only what is missing |

These restate rules of the keeper this branch generalizes (its state rules and its vault conventions) as mistakes to avoid.

## Not yet proven

- The copy of the skeleton and the creation of the state files are written, not exercised on a fresh project.
