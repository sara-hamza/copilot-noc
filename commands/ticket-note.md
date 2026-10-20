---
description: Turn what you observed and did into a clean, pasteable ticket note
argument-hint: <paste your rough notes, checks and results>
---

Run the noc-copilot skill in **ticket-note** mode on:

$ARGUMENTS

- Use the Ticket note template in `${CLAUDE_PLUGIN_ROOT}/skills/noc-copilot/references/ticket-templates.md`.
- Keep only facts the engineer provided. Do not add checks they did not mention; if an obvious check is missing, append a single line `SUGGESTED (not yet done): ...` after the note.
- Timestamps with zone (ask the zone only if ambiguous; default ACDT/ACST for Adelaide).
- Remove customer personal data unless the engineer clearly wants it in the note.
