# Windsurf adapter

Windsurf Cascade does not have a "skills" concept like Cursor. Kipi integrates via:

- **Rules file:** `.windsurfrules` (the file Cascade reads on every conversation in this repo). The install skill appends the IDE-neutral rules block from `_bmad/kipi/rules-template.md` between the managed start/end markers.

To invoke Kipi from Cascade, the user asks Cascade to "talk to Kipi" or "run /kipi-update-core" — the rules block tells the agent how to load `_bmad/kipi/<skill>/SKILL.md` and execute it inline.

This folder intentionally contains no skill stubs. The install skill writes only the rules block.
