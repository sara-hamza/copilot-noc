# Outage playbook: fault types, what the NOC sees, how to confirm and resolve

Draft of 16 Oct 2026, written against the tools the NOC actually has (see `current-tools.md`). Resolution steps are general good practice and need Network Engineering sign-off. Use this to name the fault type in LIKELY FAULT DOMAIN and to pick NEXT CHECKS that the NOC can really do.

## Quick index

| ID | Category | Fault | Typical severity |
|---|---|---|---|
| F-01 | Access network | Whole OLT down | Sev 1 if multiple communities or >1,000 users; otherwise Sev 2 |
| F-02 | Access network | PON port down (loss of signal) | Sev 2 (or Sev 3 if few services) |
| F-03 | Access network | Feeder fibre cut (several PONs at once) | Sev 1 if multiple communities or >1,000 users; otherwise Sev 2 |
| F-04 | Access network | Splitter or distribution fibre fault | Sev 3 (Sev 2 if a large building or business site) |
| F-05 | Access network | Degraded optical levels on a PON (slow / intermittent) | Sev 3 (Sev 2 if many drop-outs) |
| F-06 | Access network | Rogue ONT disrupting a PON | Sev 2–3 |
| F-07 | Access network | OLT line card failure | Sev 1 if multiple communities or >1,000 users; otherwise Sev 2 |
| F-08 | Backhaul & transport | Carrier backhaul link down | Sev 1 |
| F-09 | Backhaul & transport | Backhaul degraded (packet loss / latency) | Sev 2 |
| F-10 | Backhaul & transport | Uplink congestion (peak-hour slowness) | Sev 3 |
| F-11 | Backhaul & transport | DWDM / optical transport fault | Sev 1 |
| F-12 | IP / core network | Aggregation router down | Sev 1 |
| F-13 | IP / core network | Core router failure | Sev 1 |
| F-14 | IP / core network | Routing problem (BGP / OSPF flap, route leak) | Sev 1–2 |
| F-15 | IP / core network | Layer-2 loop / broadcast storm | Sev 1–2 |
| F-16 | IP / core network | Failed change / configuration error | Depends on impact (matrix) |
| F-17 | Subscriber platforms | RADIUS failure (customers cannot log in) | Sev 1 |
| F-18 | Subscriber platforms | DHCP failure (IPoE customers get no address) | Sev 1–2 |
| F-19 | Subscriber platforms | BNG failure | Sev 1 |
| F-20 | Subscriber platforms | Mass PPPoE session drops | Sev 2 |
| F-21 | Subscriber platforms | CGNAT or DNS issue | Sev 2–3 |
| F-22 | Site & power | Mains power loss at a cabinet or site (on battery) | Sev 2, rising to Sev 1 when batteries run out |
| F-23 | Site & power | Battery depletion / UPS failure | Sev 1 if multiple communities or >1,000 users; otherwise Sev 2 |
| F-24 | Site & power | Area mains power outage (utility) | Usually not an Opticomm fault – record and monitor |
| F-25 | Site & power | Cooling (HVAC) failure / overheating | Sev 2–3 (can escalate) |
| F-26 | Site & power | Data centre or major site loss | Sev 1 (NOC-004 example) |
| F-27 | RSP-specific | RSP handoff (NNI) down | Based on that RSP's customer count (matrix) |
| F-28 | RSP-specific | RSP VLAN or RADIUS realm misconfiguration | Sev 2–3 |
| F-29 | RSP-specific | L2TP tunnel down | Based on that RSP's customer count |
| F-30 | Systems & monitoring | Monitoring blind spot (InterMapper / feed down) | Sev 2 (NOC is blind) |
| F-31 | Systems & monitoring | RSP portal or service activation (OSS) down | Sev 2 |

## Access network

### F-01 Whole OLT down

- **What the NOC sees today:** InterMapper "NODE Down" for the OLT in Slack; Grafana MAC count for that OLT drops to ~0.
- **How to confirm:** MAC count drop matches the OLT; neighbouring OLTs on the same aggregation router are up; check if the site also lost power (F-24).
- **Typical severity:** Sev 1 if multiple communities or >1,000 users; otherwise Sev 2
- **NOC steps:** 1. Acknowledge alert and declare outage. 2. Check site power / other devices at the same site. 3. Publish RSP advisory (blocks duplicate RSP faults). 4. Page on-call (Sev 1/2). 5. Record ETR.
- **Escalate to / who fixes:** Network Engineering (remote reboot / line-card check); Field team if hardware or power.

### F-02 PON port down (loss of signal)

- **What the NOC sees today:** InterMapper may only show the OLT as up – the PON loss is often invisible; MAC count on that PON drops in Grafana.
- **How to confirm:** MAC count drop limited to one PON; EMS alarm "PON LOS" (Network Engineering can see it, NOC cannot today).
- **Typical severity:** Sev 2 (or Sev 3 if few services)
- **NOC steps:** 1. Declare with the PON selected. 2. Ask Network Engineering for the EMS alarm and optical reading. 3. Dispatch field team for fibre fault-finding. 4. Advisory to affected RSPs.
- **Escalate to / who fixes:** Field team / splicing contractor (fibre); Network Engineering (port or optic).

### F-03 Feeder fibre cut (several PONs at once)

- **What the NOC sees today:** Several PONs on one OLT lose MACs at the same time; OLT itself stays up in InterMapper.
- **How to confirm:** All affected PONs share the same feeder route / pit; often follows civil works or construction nearby.
- **Typical severity:** Sev 1 if multiple communities or >1,000 users; otherwise Sev 2
- **NOC steps:** 1. Declare one outage covering all affected PONs (multi-select). 2. Check planned works and Before You Dig requests / known civil works. 3. Dispatch splicing contractor. 4. Temporary restoration via spare fibres if available.
- **Escalate to / who fixes:** Splicing contractor; Field team.

### F-04 Splitter or distribution fibre fault

- **What the NOC sees today:** Part of a PON loses MACs (one street or building); nothing in InterMapper.
- **How to confirm:** Affected services all sit behind one splitter; others on the same PON are fine.
- **Typical severity:** Sev 3 (Sev 2 if a large building or business site)
- **NOC steps:** 1. Declare against the splitter. 2. Dispatch field team. 3. Advisory to affected RSPs.
- **Escalate to / who fixes:** Field team.

### F-05 Degraded optical levels on a PON (slow / intermittent)

- **What the NOC sees today:** No down alert; RSPs report drop-outs or slowness; MAC count dips and recovers repeatedly.
- **How to confirm:** Optical Rx power trending low; interface errors rising (Centreon / EMS data).
- **Typical severity:** Sev 3 (Sev 2 if many drop-outs)
- **NOC steps:** 1. Raise as degraded-performance outage. 2. Request optical readings. 3. Schedule fibre clean / repair as planned works where possible.
- **Escalate to / who fixes:** Field team; Network Engineering.

### F-06 Rogue ONT disrupting a PON

- **What the NOC sees today:** Whole PON flaps intermittently; MAC count saw-tooths; repeated short InterMapper alerts.
- **How to confirm:** EMS shows rogue-ONT / upstream interference alarms on the PON.
- **Typical severity:** Sev 2–3
- **NOC steps:** 1. Raise outage for the PON. 2. Network Engineering isolates the rogue ONT. 3. Field visit to replace the ONT.
- **Escalate to / who fixes:** Network Engineering; Field team.

### F-07 OLT line card failure

- **What the NOC sees today:** Several PONs on the same card drop together; OLT stays up.
- **How to confirm:** Affected PONs are all on one card / slot.
- **Typical severity:** Sev 1 if multiple communities or >1,000 users; otherwise Sev 2
- **NOC steps:** 1. Declare outage for the affected PONs. 2. Network Engineering confirms card failure. 3. Field team replaces card (spares).
- **Escalate to / who fixes:** Network Engineering; Field team.


## Backhaul & transport

### F-08 Carrier backhaul link down

- **What the NOC sees today:** Aggregation site and everything behind it goes down in InterMapper; MAC counts drop for several estates.
- **How to confirm:** Manual check on the carrier portal (cannot be automated); carrier ticket raised.
- **Typical severity:** Sev 1
- **NOC steps:** 1. Declare outage. 2. Check carrier portal and record result + carrier ticket. 3. Recheck every 30 min. 4. Page on-call. 5. Keep RSPs updated with the carrier ETR.
- **Escalate to / who fixes:** Carrier (backhaul provider); Network Engineering for any failover.

### F-09 Backhaul degraded (packet loss / latency)

- **What the NOC sees today:** No down alert; RSPs report slowness across a whole area at once.
- **How to confirm:** Uplink traffic / errors (Centreon); carrier portal shows a degradation.
- **Typical severity:** Sev 2
- **NOC steps:** 1. Raise degraded-performance outage. 2. Carrier portal check. 3. Network Engineering considers rerouting.
- **Escalate to / who fixes:** Carrier; Network Engineering.

### F-10 Uplink congestion (peak-hour slowness)

- **What the NOC sees today:** No alerts; evening slowness complaints from one area.
- **How to confirm:** Uplink traffic at or near capacity in peak hours (Centreon).
- **Typical severity:** Sev 3
- **NOC steps:** 1. Record as degraded performance. 2. Pass capacity data to Network Engineering for upgrade planning.
- **Escalate to / who fixes:** Network Engineering (capacity).

### F-11 DWDM / optical transport fault

- **What the NOC sees today:** Several sites drop together across a region.
- **How to confirm:** Common transport path between the affected sites.
- **Typical severity:** Sev 1
- **NOC steps:** 1. Declare outage. 2. Network Engineering checks transport alarms. 3. Carrier or field repair.
- **Escalate to / who fixes:** Network Engineering; carrier / field.


## IP / core network

### F-12 Aggregation router down

- **What the NOC sees today:** InterMapper "NODE Down" for the aggregation router; all OLTs behind it unreachable; MAC counts drop region-wide.
- **How to confirm:** Router unreachable; check power at the site (F-24) and backhaul (F-08).
- **Typical severity:** Sev 1
- **NOC steps:** 1. Declare outage. 2. Page on-call. 3. Check power and backhaul first. 4. Advisory to all affected RSPs.
- **Escalate to / who fixes:** Network Engineering (IP core); Field team if hardware / power.

### F-13 Core router failure

- **What the NOC sees today:** Many regions affected at once; widespread InterMapper alerts.
- **How to confirm:** Common upstream router; often after a change.
- **Typical severity:** Sev 1
- **NOC steps:** 1. Declare major outage. 2. Page on-call immediately. 3. Check recent changes. 4. Advisory to all RSPs.
- **Escalate to / who fixes:** Network Engineering (IP core); vendor support.

### F-14 Routing problem (BGP / OSPF flap, route leak)

- **What the NOC sees today:** Intermittent reachability across areas; flapping InterMapper alerts.
- **How to confirm:** Routing protocol alarms (Network Engineering).
- **Typical severity:** Sev 1–2
- **NOC steps:** 1. Raise outage. 2. Network Engineering investigates routing. 3. Roll back recent changes if linked.
- **Escalate to / who fixes:** Network Engineering.

### F-15 Layer-2 loop / broadcast storm

- **What the NOC sees today:** Sudden widespread slowness or drop-outs in one area; devices flapping.
- **How to confirm:** High traffic / CPU on switches; loop alarms.
- **Typical severity:** Sev 1–2
- **NOC steps:** 1. Raise outage. 2. Network Engineering isolates the loop. 3. Check recent cabling or changes.
- **Escalate to / who fixes:** Network Engineering.

### F-16 Failed change / configuration error

- **What the NOC sees today:** Outage starts during or just after planned works.
- **How to confirm:** Timing matches a change window (Planned works register).
- **Typical severity:** Depends on impact (matrix)
- **NOC steps:** 1. Link outage to the change. 2. Ask the change owner to roll back. 3. Record in post-incident review.
- **Escalate to / who fixes:** Change owner / Network Engineering.


## Subscriber platforms

### F-17 RADIUS failure (customers cannot log in)

- **What the NOC sees today:** Devices stay UP in InterMapper; MAC counts may look normal; sessions drop and RSPs report "PPPoE not authenticating" across many areas.
- **How to confirm:** Authentication failures on RADIUS; affects all areas served by the platform.
- **Typical severity:** Sev 1
- **NOC steps:** 1. Declare platform outage (state-wide or national). 2. Page on-call. 3. Advisory to all RSPs. 4. Systems / platform team restores RADIUS.
- **Escalate to / who fixes:** Systems / platform team.

### F-18 DHCP failure (IPoE customers get no address)

- **What the NOC sees today:** No device alerts; RSPs using IPoE report no internet; MAC present but no IP.
- **How to confirm:** DHCP server / relay errors.
- **Typical severity:** Sev 1–2
- **NOC steps:** 1. Declare platform outage scoped to affected RSPs / areas. 2. Platform team restores DHCP.
- **Escalate to / who fixes:** Systems / platform team.

### F-19 BNG failure

- **What the NOC sees today:** All sessions in a state drop; BNG down in InterMapper.
- **How to confirm:** BNG unreachable; sessions moving to standby if available.
- **Typical severity:** Sev 1
- **NOC steps:** 1. Declare outage for the state. 2. Page on-call. 3. Confirm failover. 4. Advisory to all RSPs in the state.
- **Escalate to / who fixes:** Network Engineering / platform team.

### F-20 Mass PPPoE session drops

- **What the NOC sees today:** Spike of drop-out complaints; sessions reconnect in waves.
- **How to confirm:** Session counters on BNG / RADIUS.
- **Typical severity:** Sev 2
- **NOC steps:** 1. Raise outage. 2. Platform team investigates.
- **Escalate to / who fixes:** Platform team.

### F-21 CGNAT or DNS issue

- **What the NOC sees today:** Customers connected but some sites / services do not load.
- **How to confirm:** DNS resolution or CGNAT pool errors.
- **Typical severity:** Sev 2–3
- **NOC steps:** 1. Raise degraded outage. 2. Platform team fixes.
- **Escalate to / who fixes:** Platform team.


## Site & power

### F-22 Mains power loss at a cabinet or site (on battery)

- **What the NOC sees today:** Often nothing at first – equipment runs on battery; InterMapper alerts only when batteries run out.
- **How to confirm:** Site power alarm (if monitored); utility outage map for the area.
- **Typical severity:** Sev 2, rising to Sev 1 when batteries run out
- **NOC steps:** 1. Raise outage early. 2. Check utility outage map. 3. Dispatch generator if battery time is short. 4. Advisory to RSPs before services drop.
- **Escalate to / who fixes:** Field team (generator); utility.

### F-23 Battery depletion / UPS failure

- **What the NOC sees today:** Site goes down after a power cut; many alerts at once.
- **How to confirm:** Power alarms; timing after mains loss.
- **Typical severity:** Sev 1 if multiple communities or >1,000 users; otherwise Sev 2
- **NOC steps:** 1. Declare outage. 2. Dispatch generator / field team. 3. Restore power.
- **Escalate to / who fixes:** Field team.

### F-24 Area mains power outage (utility)

- **What the NOC sees today:** Many ONTs go offline in one area; MAC count drops; no network device down.
- **How to confirm:** Utility outage map; ONT "dying gasp" (power-loss) alarms from EMS.
- **Typical severity:** Usually not an Opticomm fault – record and monitor
- **NOC steps:** 1. Record as third-party power event. 2. Tell RSPs it is a utility outage. 3. Monitor recovery when power returns.
- **Escalate to / who fixes:** Utility (no Opticomm repair needed).

### F-25 Cooling (HVAC) failure / overheating

- **What the NOC sees today:** Equipment alarms or intermittent failures at one site.
- **How to confirm:** Temperature alarms at the site.
- **Typical severity:** Sev 2–3 (can escalate)
- **NOC steps:** 1. Raise outage or warning. 2. Dispatch HVAC / field team. 3. Reduce load if needed.
- **Escalate to / who fixes:** Facilities / HVAC contractor.

### F-26 Data centre or major site loss

- **What the NOC sees today:** Everything at the site down; huge alert storm.
- **How to confirm:** Whole-site failure.
- **Typical severity:** Sev 1 (NOC-004 example)
- **NOC steps:** 1. Declare major outage. 2. Page on-call and duty manager. 3. Invoke disaster recovery. 4. Advisory to all RSPs.
- **Escalate to / who fixes:** Network Engineering; facilities; management.


## RSP-specific

### F-27 RSP handoff (NNI) down

- **What the NOC sees today:** Only one RSP's customers affected; other RSPs fine; NNI port down.
- **How to confirm:** NNI / port status for that RSP.
- **Typical severity:** Based on that RSP's customer count (matrix)
- **NOC steps:** 1. Declare outage scoped to the RSP. 2. Contact the RSP's NOC. 3. Check whether the fault is on our side or theirs.
- **Escalate to / who fixes:** Network Engineering and the RSP.

### F-28 RSP VLAN or RADIUS realm misconfiguration

- **What the NOC sees today:** One RSP's new or changed services fail; existing ones may be fine.
- **How to confirm:** Config change for that RSP; realm errors.
- **Typical severity:** Sev 2–3
- **NOC steps:** 1. Raise outage for the RSP. 2. Check recent provisioning changes. 3. Fix config.
- **Escalate to / who fixes:** Systems / provisioning; RSP.

### F-29 L2TP tunnel down

- **What the NOC sees today:** One RSP's sessions fail across all areas.
- **How to confirm:** Tunnel status.
- **Typical severity:** Based on that RSP's customer count
- **NOC steps:** 1. Raise outage for the RSP. 2. Platform team and RSP restore tunnel.
- **Escalate to / who fixes:** Platform team; RSP.


## Systems & monitoring

### F-30 Monitoring blind spot (InterMapper / feed down)

- **What the NOC sees today:** Slack goes quiet – no alerts at all, which looks like "all good".
- **How to confirm:** InterMapper itself unreachable or not posting; test alert fails.
- **Typical severity:** Sev 2 (NOC is blind)
- **NOC steps:** 1. Check InterMapper health. 2. Watch Grafana MAC counts manually meanwhile. 3. Systems team restores monitoring.
- **Escalate to / who fixes:** Systems team.

### F-31 RSP portal or service activation (OSS) down

- **What the NOC sees today:** RSPs cannot raise faults or advisories do not publish.
- **How to confirm:** Portal / OSS health check.
- **Typical severity:** Sev 2
- **NOC steps:** 1. Tell RSPs by e-mail. 2. Systems team restores the portal.
- **Escalate to / who fixes:** Systems team.

