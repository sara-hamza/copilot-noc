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

## CUSTOMISE: escalation matrix

Fill in with the company's actual severities, SLAs and contacts:

| Severity | Definition | Notify | Within |
|---|---|---|---|
| P1 | <e.g. OLT or multi-PON outage> | <duty manager, Engineering on-call> | <15 min> |
| P2 | <single PON / >N premises> | <...> | <...> |
| P3 | <single premise, no workaround> | <...> | <...> |
| P4 | <degraded, workaround exists> | <...> | <...> |
