# Fault domains for a GPON/FTTP wholesale network

Walk bottom-up. The first layer with a ✗ is the fault domain. Each domain lists the signals that point to it, the checks that confirm it, and who usually owns the fix.

## 1. Optical / physical

**Signals**: ONT offline with LOS / LOSi; ONT RX below range or OLT RX from ONT below range; dying gasp then no return (power) vs LOS without dying gasp (fibre); BER/FEC errors rising; intermittent registration (flapping).

**Discriminate single vs distribution**:
- Adjacent ONTs on the same splitter also down → distribution (splitter, FDH, feeder, OLT port, OLT card).
- Only this ONT → drop/lead-in, premises connector, ONT PSU, ONT hardware.
- Whole PON port → OLT port / SFP / card / feeder cable.
- Many PONs on one OLT → OLT, power, uplink.

**Checks**: current RX at ONT and OLT; ONT uptime/last-down reason (DG vs LOS); adjacent ONT status; alarm history for the PON; any planned work or outage on the PON/OLT; power at the premises (customer confirms ONT lights).

**Owner**: Field (drop/lead-in/splitter/FDH), NOC (OLT port/SFP), customer (power, ONT location).

## 2. ONT / UNI / customer cabling

**Signals**: ONT online, good RX, but UNI/ETH down; wrong ONT serial/profile; ONT shows "unprovisioned" or in discovery; ONT model mismatch with service profile.

**Checks**: UNI port state and negotiated speed/duplex; cable replaced; different device plugged in; ONT provisioned profile vs expected; ONT firmware version vs approved list.

**Owner**: customer/RSP (CPE and cabling), Provisioning (profile), Field (ONT swap).

## 3. Layer 2 / VLAN / provisioning

**Signals**: UNI up but no MAC learned; MAC learned on the wrong VLAN; GEM port / T-CONT missing; storm control or MAC limit triggered; loop detected; service port mismatch between OLT and aggregation.

**Checks**: MAC table on the service port (S-VLAN/C-VLAN); service-port / GEM / T-CONT mapping vs order; aggregation switch VLAN on the OLT uplink; error counters on uplink; recent provisioning change timestamps.

**Owner**: Provisioning / Engineering.

## 4. Session / authentication (PPPoE or DHCP)

**Signals**: MAC learned, VLAN correct, but no PPPoE session; PADI seen but no PADO (RSP BNG not responding / wrong VLAN to RSP); PADO but auth fails (credentials, radius); DHCP discover without offer; session established then drops (keepalive, MTU, BNG reload).

**Checks**: capture or counters for PADI/PADO/PADR/PADS on the service port; whether PADI leaves the CPE (CPE log or ONT counters); C-VLAN/S-VLAN against the RSP's aggregation VLAN; other customers of the same RSP on the same OLT (RSP-wide vs single); ask RSP for BNG session details, radius reject reason, last successful session time.

**Owner**: RSP (BNG, radius, credentials), Engineering (aggregation to RSP NNI), customer (CPE credentials).

## 5. Service / traffic quality

**Signals**: session up but no traffic; one direction only; slow speed; packet loss; high latency; IPTV pixelation (multicast); voice one-way audio.

**Checks**: counters and discards on UNI, GEM, uplink; DBA/T-CONT bandwidth profile vs sold speed; PON utilisation; multicast group joins; MTU; RSP upstream status; customer Wi-Fi vs wired test.

**Owner**: RSP (core/upstream/CPE), Engineering (bandwidth profiles, congestion), customer (LAN/Wi-Fi).

## Quick patterns

| Paste looks like | Most likely |
|---|---|
| LOS, no dying gasp, RX none, neighbours fine | Drop/lead-in or premises fibre |
| Dying gasp then offline | Power at premises |
| Whole PON LOS at once | Feeder/splitter/OLT port; check planned works |
| RX -29 dBm and intermittent | Degraded fibre/connector; borderline optical |
| ONT online, UNI down | Cable / CPE Ethernet |
| UNI up, no MAC | VLAN mapping or CPE not sending |
| MAC up, no PPPoE | RSP BNG / auth / VLAN to RSP |
| PPPoE up, no traffic | RSP routing / CPE / default route |
| Many tickets, same RSP, many OLTs | RSP-side outage; escalate to RSP, inform duty manager |
| Many tickets, one OLT/area | Network operator outage; check power/uplink, raise incident |
