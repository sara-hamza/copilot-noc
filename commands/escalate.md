---
description: Draft an escalation to Field, Engineering, Provisioning or the RSP with exactly the evidence they need
argument-hint: <to:field|engineering|provisioning|rsp> <paste evidence and what you have ruled out>
---

Run the noc-copilot skill in **escalate** mode on:

$ARGUMENTS

1. Determine the recipient from a `to:` token or from context; if unclear, choose based on the fault domain and state the choice in the first line.
2. Use the Escalation template and the "What each recipient usually needs" table in `${CLAUDE_PLUGIN_ROOT}/skills/noc-copilot/references/ticket-templates.md`.
3. WHAT WE HAVE RULED OUT must only list domains the engineer's input actually clears.
4. WHAT WE NEED FROM YOU: numbered, specific, answerable. For RSP escalations use neutral, evidence-led wording (writing-helper tone table).
5. Add a `MISSING BEFORE SENDING` line if a required item (service identifier, timestamps, RX values) is absent.
