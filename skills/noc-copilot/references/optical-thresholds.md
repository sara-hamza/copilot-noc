# Optical thresholds (defaults; CUSTOMISE to company standards)

These are common GPON (Class B+ / C+) working ranges. Replace with the company's engineering standard once confirmed; the triage logic references this file.

| Measurement | Good | Warning (investigate) | Fault |
|---|---|---|---|
| ONT RX (1490 nm downstream) | -8 to -24 dBm | -24 to -27 dBm | below -27 dBm, or no signal / LOS |
| OLT RX from ONT (1310 nm upstream) | -8 to -26 dBm | -26 to -28 dBm | below -28 dBm |
| ONT TX | +0.5 to +5 dBm | outside | outside with RX fine → ONT laser |
| OLT TX (per PON) | +1.5 to +5 dBm (B+), +3 to +7 dBm (C+) | outside | SFP |
| Optical budget used | < design budget - 1 dB margin | near budget | over budget |

Interpretation notes:
- ONT RX stronger than -8 dBm suggests a missing attenuation stage or wrong splitter ratio; it can cause receiver saturation errors.
- A sudden 2-3 dB drop versus the installation record indicates a new event (bend, water in pit, connector) even if still "in range".
- Compare with adjacent ONTs on the same splitter: all shifted → upstream of the splitter; only one shifted → drop.
- Temperature and dirty connectors can move readings by ~1 dB; treat borderline values as "warning", not "fault".

## CUSTOMISE: company-specific values

Edit this section with the real numbers from the engineering standard and the NMS alarm thresholds:

- ONT RX major alarm threshold: `-27 dBm` (verify)
- ONT RX minor alarm threshold: `-25 dBm` (verify)
- Expected ONT RX at install, typical estate: `-14 to -22 dBm` (verify)
- Splitter ratios in use: `1:32` (verify; note any 1:64 or cascaded 1:4 + 1:8 areas)
- OLT platforms and the exact CLI commands used to read RX/TX (so the copilot can recognise the output format):
  - `<platform>`: `<command>` (fill in)
