# Start here

> Modified on the branch `onegate` of `LairdLuca/kipi-agent`: the workshop installs this branch, BMad is optional, and the install asks before it writes.

## Workshop participants

1. Read [workshop/1-READ-ME-FIRST.txt](workshop/1-READ-ME-FIRST.txt)
2. Copy all of [workshop/2-INSTALL-PROMPT.txt](workshop/2-INSTALL-PROMPT.txt) into a new IDE chat in your project
3. After install, use [workshop/3-AGENT-BUILDER-PROMPT.txt](workshop/3-AGENT-BUILDER-PROMPT.txt) for the custom-agent exercise (it needs BMad: say yes to it in the install chat)

No advance setup required: the install prompt walks you through Node.js and Kipi, and through BMad if you want it. It installs this branch of Kipi (`onegate`), not upstream's `main`.

## Manual install (developers)

Prerequisites: git, and an editor. BMad is optional.

1. Copy everything under `agent_files/` into one folder of your project, the Kipi folder: `_bmad/kipi/` if you use BMad, otherwise `.kipi/agent/`
2. Fill the placeholders in the copied files, then copy the `kipi` stub of your editor from the filled Kipi folder (not from the clone): see "Install by hand" in [README.md](README.md)
3. Open a new chat and run `/kipi`: Kipi shows an install plan and asks before it writes anything, then installs its stubs, its commands and its rules block (or pick **[IN]** from the menu to re-run)

See [README.md](README.md) for full usage and knowledge layout.
