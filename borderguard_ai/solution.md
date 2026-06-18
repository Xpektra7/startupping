# Solution: Product Architecture & MVP

## The Core Innovation — Analog Wake Front-End

The entire system is built around a **discrete BJT/comparator analog trigger** that serves as the hardware root of trust for power management.

```
PIR sensor → discrete BJT amplification → 
  → comparator with hysteresis (human IR mass + slew-rate filter)
    → MOSFET gate drive → power rail enable for camera + SoC
```

### How it works

1. **PIR sensor** detects a heat-source event in its field of view.
2. **Two-stage BJT amplifier** boosts the microvolt-level signal while filtering slow thermal drift (sun heating a rock) and fast noise (wind shaking foliage).
3. **Comparator** with external resistor-set thresholds evaluates:
   - IR signature **mass** (is it human-sized or cow-sized?)
   - **Slew rate** (a person walks at ~1–2 m/s; an animal bolts faster)
   - **Duration** (transient vs. sustained presence)
4. Only when all three criteria are met does the comparator **drive a MOSFET** that switches on the +5V and +3.3V rails to the camera module and AI compute unit.
5. The digital stack boots, grabs a frame, runs the inference, and — if confirmed — fires an SMS alert.

### Why this matters

| Approach | Idle current | False wakes/day | Field life (10W solar, 20Ah battery) |
|---|---|---|---|
| Always-on camera (motion detect) | ~150 mA | 200+ (insects, shadows) | 4–6 days |
| Periodic wake (every 5 min) | ~20 mA | N/A (blind window) | 2–3 weeks |
| **Analog trigger (this design)** | **<5 µA** | **~2–3 (genuine-unique events)** | **4–6 months** |

**Key parameters of the analog stage:**
- Quiescent current: <5 µA (single-supply, 3.3V)
- Comparator hysteresis: adjustable, typically 200–500 mV
- IR mass threshold: set by bias network for ~50–80 kg human target at 10 m
- Slew-rate window: 0.5–3 m/s (excludes sprinting wildlife and slow thermal drift)
- Response time: <200 ms from IR event to rail enable

The circuit uses a standard **LM358** dual op-amp (first stage: amplifier, second stage: comparator with positive feedback for hysteresis) and a handful of passives — total BOM add: <$0.80.

---

## Hardware Stack

- **Camera:** Off-the-shelf trail camera module (motion-capable, IR-LED)
- **Compute:** Low-power MCU + edge TPU or equivalent (e.g. Raspberry Pi Zero + Coral)
- **Analog trigger:** LM358 + BJT front-end + PIR element (custom PCB, ~$4 BOM)
- **Power:** 10W foldable solar panel + 20 Ah LiFePO4 battery + TP4056 charger
- **Comms:** 4G LTE Cat-M1 module (or LoRa where cellular is absent)
- **Enclosure:** IP65 weatherproof, vandal-resistant locks

Hardware is provisioned to partners **at-cost**. BorderGuard AI does not make margin on hardware.

---

## Software Stack

- **Edge AI:** YOLO-based lightweight model (TensorFlow Lite Micro), quantized to INT8, trained on IR/grayscale trail-camera footage with a single class: "human"
- **Alert pipeline:** MQTT over cellular → cloud function → Twilio SMS
- **Health telemetry:** Daily battery voltage, cell signal, trigger count (reported in <10 bytes via SMS or LoRa)
- **Firmware:** Bare-metal C on the MCU for the analog trigger supervision; Linux on the SoC for camera capture + inference

---

## MVP

One fully assembled unit deployed on a high-traffic private border ranch:

- Analog trigger tuned to human signature
- AI model trained on ~500 labeled trail-camera images from the deployment area
- SMS alert forwarded to the landowner's phone
- No dashboard, no CRM, no mobile app — just alert or no alert

Validation criterion: **>90% human detection rate with <3 false alerts per week** over a 30-day trial.
