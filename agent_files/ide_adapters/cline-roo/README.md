# Cline / Roo Code adapter

Cline and Roo read repo-level rules from `AGENTS.md` (or a similar generic agent rules file). Kipi integrates via:

- **Rules file:** `AGENTS.md` (the file the agent reads on every conversation in this repo). The install skill appends the IDE-neutral rules block from `_bmad/kipi/rules-template.md` between the managed start/end markers.

To invoke Kipi from Cline / Roo, the user asks the agent to "talk to Kipi" or "run /kipi-update-core" — the rules block tells the agent how to load `_bmad/kipi/<skill>/SKILL.md` and execute it inline.

This folder intentionally contains no skill stubs. The install skill writes only the rules block.
