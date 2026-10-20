# What the NOC can and cannot see today (confirmed 16 Oct 2026)

Use this before writing NEXT CHECKS. Never ask the engineer to "read the EMS alarm" or "check optical levels" as if it were a click away; say who holds that data and what the NOC can do meanwhile.

| Data | Where it is | NOC access |
|---|---|---|
| Device up/down alerts | InterMapper, posted into a Slack channel ("router NODE Down: <name> Map: <map> Node: <name> (<IP>) Uptm: ...") | Yes, but up/down only, no cause, no grouping, no acknowledgement |
| Network maps / topology | InterMapper | Yes (devices only, no customer counts) |
| Customers online per device | Grafana MAC-count graphs | Yes, read by eye; not linked to the ticket |
| OLT/EMS alarms (PON LOS, ONT dying gasp), optical power, interface errors, uplink traffic | Some in Centreon, the rest with Network Engineering | **No.** Ask Network Engineering; Centreon data is being added to Grafana |
| Backhaul link status | Each carrier's own portal | Manual check only; cannot be automated |
| After-hours callout | Email that triggers Opsgenie (Opsgenie end of support 5 Apr 2027) | Yes, manual |
| Incident record | OTT ticket | Yes; shows creator but not current owner |
| RSP advisories and RSP fault tickets | RSP portal | Yes; RSP faults for services inside an active advisory are to be blocked (proposed rule) |
| Shift handover | Written post in Slack | Yes |

## How this changes triage

- A fault that only shows as "NODE Down" with nothing else is normal here. Rank causes from MAC-count behaviour, which other devices went down at the same time, planned works, and the carrier portal.
- "Silent" outages (RADIUS, DHCP, PON loss, utility power cut) never raise an InterMapper alert. If MAC counts drop or several RSP faults arrive for one area while every device is "up", treat that as the alert.
- When the discriminating check needs EMS/optical data, write it as "ask Network Engineering for ..." and give the NOC check that can run in parallel.
- A quiet Slack channel can mean InterMapper has stopped sending. If no alerts for longer than normal, check InterMapper health and watch MAC counts manually.
