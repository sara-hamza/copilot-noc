---
description: Full NOC triage from pasted CLI output, alarms, screenshots or ticket text - fault domain, evidence, next checks, ticket note
argument-hint: <paste evidence, or drop a screenshot and describe the symptom>
---

Run the noc-copilot skill in **triage** mode on this input:

$ARGUMENTS

Steps:
1. If an image was provided, transcribe it first (screenshot-reader) into a READ FROM IMAGE block.
2. Walk the layers bottom-up using `references/fault-domains.md` and `references/optical-thresholds.md`.
3. Output the full Triage template: LIKELY FAULT DOMAIN, CONFIDENCE, EVIDENCE (✓/✗/?), NEXT CHECKS, IF THAT SHOWS ... THEN ..., SUGGESTED TICKET NOTE.
4. If adjacent-ONT status or current optical levels are missing, make them NEXT CHECK 1 and say why.
5. If evidence is too thin to name a domain, say DO NOT CONCLUDE YET and give the single most discriminating check.
