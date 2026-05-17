# Generic adapter (fallback)

Used when the user's IDE is `other` and Kipi can't auto-detect the right skill / rules conventions. The install skill will:

1. Ask the user three questions:
   - Where does this IDE store agent rules? (path to a rules file or `none` if there isn't one)
   - Does this IDE have a skill / agent definition folder? (path or `none`)
   - Does this IDE support slash-command files? (path or `none`)
2. Use the answers to write only what's possible. If the IDE has none of the three, Kipi still works via direct invocation (`load _bmad/kipi/SKILL.md and act as Kipi`).

The rules block written is the IDE-neutral version from `_bmad/kipi/rules-template.md`, bracketed by the managed start/end markers so it can be safely refreshed.
