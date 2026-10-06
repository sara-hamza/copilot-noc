---
description: Build an end-of-shift handover from your notes (and sticky notes if installed)
argument-hint: [paste shift notes] [topic:<topic>]
allowed-tools: ["Bash", "Skill"]
---

Run the noc-copilot skill in **handover** mode.

Input: $ARGUMENTS

1. If the sticky-notes plugin is installed (the `sticky-notes:actions` and `sticky-notes:bundle` skills are available), invoke `sticky-notes:actions` first and then `sticky-notes:bundle <topic>` for each ticket or topic mentioned in the pasted notes. If they are not available, continue with the pasted notes only and say so in one line.
2. Produce the Shift handover template from `references/ticket-templates.md`: ACTIVE INCIDENTS, WATCHING, WAITING ON, PLANNED WORK IN WINDOW, NOTES FOR ONCOMING SHIFT.
3. Every row needs a next action and an owner; if unknown, write `owner: TBC` rather than guessing.
4. Offer (one line) to save the handover as a sticky note on topic `handover`.
