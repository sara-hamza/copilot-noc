# Ticket, handover and escalation templates

All plain text. Timestamps with zone. No customer personal data unless the engineer includes it on purpose.

## Ticket note

```
SUMMARY: <symptom> - <likely domain> - <current status>
OBSERVED (<time zone>):
  <fact with value/unit>
  <fact>
CHECKS DONE:
  1. <check> -> <result>
  2. <check> -> <result>
FINDING: <likely cause, with confidence word>
NEXT: <owner> to <action> by <time if known>
```

## Shift handover

```
HANDOVER <date> <shift> -> <shift>

ACTIVE INCIDENTS
  <ref> | <area/OLT/RSP> | <impact> | <status> | <next action, owner, due>

WATCHING
  <ref> | <what to watch for> | <trigger to act>

WAITING ON
  <ref> | <who> | <what> | <since when>

PLANNED WORK IN WINDOW
  <ref> | <window> | <impact>

NOTES FOR ONCOMING SHIFT
  <anything non-obvious: workarounds, contacts, known NMS quirks>
```

## Escalation

Choose the recipient block, then fill. Include only what the recipient needs to act.

```
ESCALATION TO: <Field | Engineering | Provisioning | RSP>
REF: <ticket>
IMPACT: <premises affected / service type / since when>

WHAT WE SEE
  <evidence, bottom-up, with values>

WHAT WE HAVE RULED OUT
  <domains cleared and how>

WHAT WE NEED FROM YOU
  1. <specific request>
  2. <specific request>

URGENCY: <why now; SLA or customer impact>
CONTACT: <NOC desk / engineer on shift>
```

### What each recipient usually needs

| Recipient | Must include |
|---|---|
| Field | Site/estate, pit/FDH/splitter if known, ONT RX/OLT RX, adjacent ONT status, access notes, test required (OTDR, power meter), photos requested |
| Engineering | OLT/PON/service-port, config excerpts, counters, change history, what you want verified |
| Provisioning | Order/service ID, expected vs actual profile/VLAN, ONT serial |
| RSP | Service identifier they use, session evidence (MAC learned, PADI/PADO counters, timestamps), what you have cleared on the access side, the exact questions (session state, assigned IP, radius result, BNG), and what you will do once they answer |

## Severity matrix and escalation (proposed Oct 2026, not yet agreed)

Severity definitions are Operations' wording (NOC-004). Targets are Operations' proposed values from the outage-management review of 16 Oct 2026 and are **draft** until the prioritisation workshop agrees them. Scope is multi-customer outages only; single-customer faults are out of scope. ACMA classification is not used as a severity.

| Severity | Definition (from NOC-004) | Examples | Acknowledge alert | Engineer callout | First RSP advisory | RSP update every | ETR recorded within | Restore target | Warn / escalate at | Post-incident review |
|---|---|---|---|---|---|---|---|---|---|---|
| Sev 1 | Complete service outage or major business impact to multiple communities, or more than 1,000 users. | Loss of 2 or more communities (e.g. 2 communities in Darwin, 10 communities in NSW); loss of a data centre; core / aggregation router down. | 5 min | Immediately (24/7) | 15 min | 30 min | 30 min | 4 hours | 75% and 100% of restore target | Required |
| Sev 2 | Significant degradation or partial outage affecting many users: one community or fewer than 1,000 users. | One community down; an OLT or PON group down; widespread slow speeds in one area. | 10 min | Within 30 min (24/7) | 30 min | 1 hour | 1 hour | 8 hours | 75% and 100% of restore target | Required |
| Sev 3 | Limited business impact; a workaround is available. | Redundant link down with traffic rerouted; a single PON degraded but working; monitoring feed lost. | 30 min | Business hours | 2 hours (if RSPs affected) | 4 hours | 4 hours | 24 hours | 100% of restore target | Optional |
| Sev 4 | Minor impact or cosmetic issue. | A single non-service-affecting alarm; a labelling or display issue. | 4 business hours | Not required | Not required | Daily | 1 business day | 5 business days | 100% of restore target | Not required |

### Escalation ladder (proposed)

| Time since declaration | Sev 1 | Sev 2 |
|---|---|---|
| 0 min | Page Network on-call (automatic from the ticket; today: email to Opsgenie) | Page Network on-call |
| 5 min, no acknowledgement | Page backup on-call | Page backup on-call |
| 15 min | Duty Manager notified; Communications Lead assigned; RSP advisory published | — |
| 30 min | NOC Manager takes Incident Commander; ETR recorded | Duty Manager notified; RSP advisory published |
| 1 h without cause | Head of Operations; Legal and Advisory Board for a Major outage | NOC Manager |
| 2 h without ETR, or ETR passed | Executive on-call | Head of Operations |
| 75% / 100% of restore target | Warning / escalate to Head of Operations | Warning / escalate to NOC Manager |

Names live in the on-call roster, not here. Roles: Incident Commander (Senior NOC; NOC Manager for Sev 1), Technical Lead (Network Engineering on-call), Communications Lead (second NOC operator), Scribe, Change Manager, Duty Manager. On a Sev 1 nobody holds two roles.

### Backhaul

Backhaul status cannot be read automatically: check the carrier portal within 15 min of declaration, record the result and carrier ticket number on the ticket, recheck every 30 min until restored.
