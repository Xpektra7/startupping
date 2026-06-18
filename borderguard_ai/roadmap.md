# Execution Roadmap — 12-Month Incubation

## Phase 0: Analog Trigger Prototype (Month 0 — concurrent)

*Before any software is written, the analog wake front-end is breadboarded and characterized.*

**Deliverables:**
- LM358-based amplifier + comparator stage on perfboard
- Measured: quiescent current (<5 µA), trigger thresholds, hysteresis band
- PIR-to-MOSFET enable timing verified on oscilloscope
- Bill of materials frozen for PCB layout

---

## Phase 1: MVP (Months 1–3)

- Design and fab a compact 4-layer PCB integrating the analog trigger, MCU, and power regulation
- Port YOLO-based inference model to TensorFlow Lite Micro, train on ~500 local trail-camera images
- Deploy **1 unit** on a high-traffic private border ranch
- Establish end-to-end alert pipeline: PIR → analog trigger → camera capture → inference → SMS
- **Go/no-go:** >90% human detection rate, <3 false alerts/week over 30 days

---

## Phase 2: Alpha (Months 4–6)

- Scale to **5 private ranches** across different terrain types (desert, brush, wooded corridor)
- Gather diverse IR signature data to generalize the analog trigger threshold tuning
- Lock down final BOM and PCB layout for manufacturing (target: $85 BOM at qty 100)
- Refine GTM messaging: budget-constrained procurement officers, not IT managers
- Begin health telemetry collection (battery, trigger count, uptime)

---

## Phase 3: Beta (Months 7–9)

- Launch paid pilot programs with **2 county sheriff departments** (5–10 units each)
- Deliver web dashboard for agency-level fleet view
- Enforce strict feature freeze — no new detection modes, no mobile app, no analytics
- Measure and publish **verified detections/week** as the core metric
- Target: <2% monthly hardware failure rate

---

## Phase 4: Scale (Months 10–12)

- Convert agency pilots into annual SaaS contracts
- Form strategic distribution partnership with a major trail camera OEM (pre-integrated analog trigger)
- Reduce CAC through referral incentives and regional law-enforcement conferences
- Target: 200 paying camera feeds, <5% monthly churn, 90% gross margin on subscription

---

## Key Risks & Mitigations

| Risk | Mitigation |
|---|---|
| Analog trigger false-positive rate too high in high-wind/dust | Slew-rate filter + adaptive hysteresis via firmware-trimmed resistor DAC |
| Cellular coverage gaps in remote border zones | LoRa fallback for telemetry; SMS relay via mesh or satellite backpack |
| Wildlife triggering the analog classifier | Train AI on negative class (deer, coyote, cattle); use IR mass threshold to block small animals at the analog stage |
| Solar panel theft | Enclosure tamper switch triggers immediate SMS alert + disables power output |

---

## Generated Assets

- `borderguard_ai_roadmap.pdf` *(deprecated — see this document)*
- `EEE_2022_103_niche_analysis.pdf` *(deprecated — see problem.md)*
