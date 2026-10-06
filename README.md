# copilot-noc

## What it does

NOC Copilot: fault-domain analysis, next checks and ticket notes from pasted CLI output, alarms and screenshots.

Part of the **department-copilots** suite: local-only copilots where the employee provides the input, Claude analyses it on the machine, and the employee decides what to do. No CRM, NMS, email or system integrations.

## Install

```bash
claude plugin marketplace add sara-hamza/department-copilots
```

```bash
claude plugin install noc@department-copilots
```

Paste what you can see (CLI output, alarm text, a screenshot, the ticket) and get back a fault-domain call, evidence table, next checks and a ticket note you can paste.

| Command | Use when |
|---|---|
| `/noc:triage <paste>` | New fault. Full analysis: fault domain, evidence, next checks, ticket note |
| `/noc:fault-domain <paste>` | Only want the domain call and why |
| `/noc:ticket-note <paste or summary>` | Turn what you did into a clean ticket note |
| `/noc:handover` | End of shift: structured handover from your notes / sticky notes |
| `/noc:escalate <paste>` | Draft an escalation to Engineering, Field or the RSP with the evidence they need |

Requires `copilot-common`. Works well with `sticky-notes` (`/sticky-notes:bundle ticket-48812` before `/noc:triage`).

Reference knowledge lives in `skills/noc-copilot/references/`. The optical thresholds and escalation matrix there are sensible defaults marked `CUSTOMISE`; tune them to the company's standards.
