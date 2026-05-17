# VSCode + GitHub Copilot adapter

Copilot Chat does not have a "skills" concept. Kipi integrates via:

- **Rules file:** `.github/copilot-instructions.md` (the file Copilot Chat reads on every conversation in this repo). The install skill appends the IDE-neutral rules block from `_bmad/kipi/rules-template.md` between the managed start/end markers.
- **Optional prompt files:** `.github/prompts/*.prompt.md` (preview feature). Not auto-installed; if a user wants slash-command-like access, they can copy from the `prompts/` sub-folder here.

To invoke Kipi's logic from Copilot, the user asks Copilot to "talk to Kipi" or "run /kipi-update-core" — the rules block tells Copilot how to load `_bmad/kipi/<skill>/SKILL.md` and execute it inline.

This folder intentionally contains no skill stubs. The install skill writes only the rules block.
