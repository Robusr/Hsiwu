# Theory of Operation — Hsiwu Electronics

## Overview

The Hsiwu control system generates three synchronized, independent
reciprocating motor drive signals using purely analog and digital logic
components — no microcontroller, no firmware, no programmable logic.

## Block Diagram

```
                   +-----------+
  3.7V LiPo ──────│ POWER     │──── 7.35V ────────────── Motor Rail (L293D VCC2)
                   │ SUPPLY    │
                   │           │──── 5.0V ───┬────────── Logic Rail (NE555, 74HC04, L293D VCC1)
                   +-----------+             │
                                             │
              ┌──────────────────────────────┤
              │                              │
       ┌──────┴──────┐               ┌──────┴──────┐
       │ NE555 (U1)  │               │ NE555 (U2)  │  ...
       │ Astable     │               │ Astable     │
       │ ~4.8 Hz     │               │ ~4.8 Hz     │
       └──────┬──────┘               └──────┬──────┘
              │ OUT1                        │ OUT2
       ┌──────┴──────────────────────┐     │
       │       74HC04 (U5)           │     │
       │  ┌─ NOT ─ /OUT1             │     │
       │  ├─ NOT ─ /OUT2             │◄────┘
       │  └─ NOT ─ /OUT3             │
       └──────┬──────────────────────┘
              │
    ┌─────────┼─────────┐
    │   OUT1  │  /OUT1  │
    ▼         ▼         ▼
  ┌──────────────┐
  │ L293D (U6)   │──── Motor 1 (Leading Edge)
  │ Half-Bridge  │
  └──────────────┘
  (same for U7/Motor 2/Middle, U8/Motor 3/Trailing)
```

## Stage 1: Power Supply

### Input Protection
- **D1** (Schottky diode, SMC package) — reverse-polarity protection. If the
  battery is connected backwards, D1 blocks current flow.
- **SW2** (SS12D07VG4 slide switch) — master power on/off.
- **LED1** (green 0805) — power-on indicator with current-limiting resistor.

### Boost Converter (MT3608, U4)
The MT3608 is a fixed-frequency (1.2 MHz) current-mode boost converter.

- **Input**: 3.7 V (single-cell LiPo, nominal)
- **Output**: 7.35 V (set by feedback resistor divider)
- **Inductor** (L1): SMD shielded power inductor, 7.3×6.8 mm
- **Input capacitor**: 10 µF (C1, 0805 X7R or X5R)
- **Output capacitor**: 22 µF (C3, SMD aluminum electrolytic equivalent)

The output voltage is given by:
```
V_OUT = V_REF × (1 + R1/R2)
```
where V_REF ≈ 0.6 V (MT3608 internal reference), and R1, R2 form the feedback
divider.

### LDO Regulator
- A linear regulator drops the 7.35 V boost output to a clean 5.0 V rail for
  the logic ICs (NE555, 74HC04) and L293D logic supply (VCC1).
- Decoupling capacitors (C2, C4) are placed close to the IC power pins.

## Stage 2: Timing Generation (NE555 Astable)

Each NE555 (U1, U2, U3) is configured in **astable multivibrator** mode.
In this mode, the output oscillates continuously between HIGH (≈ VCC) and
LOW (≈ 0 V) with no external trigger.

### Circuit Configuration

```
           VCC
            │
           ─┼─ R_A ──┬── R_B ──┬── VCC
            │        │         │
            │        ├── Pin 7 (DISCH)
            │        │
            ├────────┼── Pin 6,2 (THRES, TRIG)
            │        │
            │       ─┼─ C
            │        │
           ─┴────────┴── GND
```

### Timing Equations

**Output HIGH time (t_H):**
```
t_H = 0.693 × (R_A + R_B) × C
```

**Output LOW time (t_L):**
```
t_L = 0.693 × R_B × C
```

**Period:**
```
T = t_H + t_L = 0.693 × (R_A + 2·R_B) × C
```

**Frequency:**
```
f = 1/T = 1.44 / ((R_A + 2·R_B) × C)
```

### Design Values (for ~4.8 Hz)

| Parameter | Value | Notes |
|-----------|-------|-------|
| R_A | ~10 kΩ (fixed + trimmer) | R + R4 (trimmer) for U1 |
| R_B | ~100 kΩ | Fixed 0805 resistor |
| C | ~1 µF | Ceramic or tantalum |
| **Target f** | ~4.8 Hz | ≈ 10 Hz reference scaled down |
| **Target T** | ~208 ms | |
| **Duty cycle** | ~52% | Slightly asymmetric by design |

**Frequency fine-tuning:** Each NE555 stage has an adjustable resistor (R4 for
U1, R10 for U2, R11 for U3) — 3-terminal SMD trimmer potentiometers. Adjusting
these changes R_A and therefore the oscillation frequency, allowing per-segment
timing calibration.

### Start-Up Synchronization

All three NE555s share the same VCC rail and have similar RC networks. They
tend to power up nearly synchronized, but small component tolerances cause
them to drift. The 74HC04 stage ensures that regardless of relative phase,
each motor always has complementary drive signals.

## Stage 3: Phase Inversion (74HC04)

The 74HC04 is a hex inverter (six independent NOT gates). Only three gates
are used:

| Gate | Input | Output | Purpose |
|------|-------|--------|---------|
| 1A → 1Y | NE555 U1 OUT | /OUT1 | Inverted drive for L293D U6 |
| 2A → 2Y | NE555 U2 OUT | /OUT2 | Inverted drive for L293D U7 |
| 3A → 3Y | NE555 U3 OUT | /OUT3 | Inverted drive for L293D U8 |

Each NE555 output is connected to its corresponding L293D input pin **and** to
a 74HC04 input. The inverted output goes to the complementary L293D input pin.

### Signal Relationships

```
NE555 OUT = HIGH → L293D IN1 = HIGH, IN2 = LOW  → Motor FORWARD
NE555 OUT = LOW  → L293D IN1 = LOW,  IN2 = HIGH → Motor REVERSE
```

This creates the continuous forward/reverse reciprocation.

### Unused Gates

The three unused inverter gates (4A/4Y, 5A/5Y, 6A/6Y) should have their inputs
tied to GND to prevent floating-input oscillation and excess current draw.

## Stage 4: Motor Drive (L293D)

The L293D is a dual H-bridge driver capable of 600 mA per channel (1.2 A peak).

### Pin Configuration (per motor, using one half-bridge)

| L293D Pin | Connection | Purpose |
|-----------|------------|---------|
| VCC1 (pin 16) | 5 V rail | Logic supply |
| VCC2 (pin 8) | 7.35 V rail | Motor power supply |
| 1,2EN (pin 1) | 5 V (always enabled) | Half-bridge 1-2 enable |
| 1A (pin 2) | NE555 OUT | Input A — direction control |
| 2A (pin 7) | 74HC04 /OUT | Input B — complementary direction |
| 1Y (pin 3) | Motor terminal + | Output to motor |
| 2Y (pin 6) | Motor terminal − | Output to motor |
| GND (pins 4,5,12,13) | Common ground | Power + logic return |

### Truth Table

| 1A (IN1) | 2A (IN2) | Motor |
|----------|----------|-------|
| H | L | Forward (7.35 V across motor) |
| L | H | Reverse (−7.35 V across motor) |
| L | L | Brake (motor terminals shorted to GND) |
| H | H | Brake (motor terminals shorted to VCC2) |

In normal operation, 1A and 2A are always complementary, so the motor is
always driving forward or reverse — never braking. Brake states only occur
for a few nanoseconds during logic transitions.

### Built-in Protection

The L293D includes:
- **Flyback diodes** on all outputs — no external diodes needed
- **Thermal shutdown** — protects against over-temperature
- **Over-current limiting** — internal current limiting

## System-Level Timing

The complete signal chain for one wing segment:

```
t = 0:    NE555 OUT goes HIGH
          → L293D IN1 = HIGH, IN2 = LOW
          → Motor drives FORWARD
          → Wing segment moves UP (flapping)

t = t_H:  NE555 OUT goes LOW (~108 ms later, at ~52% duty)
          → L293D IN1 = LOW, IN2 = HIGH
          → Motor drives REVERSE
          → Wing segment moves DOWN (return stroke)

t = T:    NE555 OUT goes HIGH again (~208 ms total)
          → Cycle repeats at ~4.8 Hz
```

## Design Rationale

### Why NE555?

- **Simplicity** — three external components (R_A, R_B, C) set the frequency
- **Wide supply range** — 4.5 V to 16 V, compatible with 5 V logic rail
- **Sufficient drive** — can source/sink 200 mA, more than enough for driving
  74HC04 inputs and L293D logic inputs
- **Well-characterized** — the NE555 is one of the most documented ICs ever made

### Why 74HC04 Instead of Direct Complementary NE555 Outputs?

The NE555 output stage is a totem-pole (push-pull) — it only provides a single
output. A complementary output would require an external transistor inverter
or logic gate. The 74HC04 provides six inverters in one package, using only
three for the three NE555 channels — clean and compact.

### Why ~4.8 Hz (Instead of the Reference Paper's ~10 Hz)?

The reference prototype (Qi & Zhang, 2020) achieves ~10 Hz flapping. The Hsiwu
design targets ~4.8 Hz — about half the frequency — for several reasons:

1. **Larger amplitude per stroke** — slower flapping allows the wing to reach
   its full 70° stroke without aerodynamic damping cutoff
2. **Reduced mechanical stress** — lower cyclic loading on the 3D-printed
   linkages and frame
3. **Observability** — at 4.8 Hz, the segmented membrane deformation is
   visually distinguishable
4. **Motor current headroom** — lower frequency means lower average current
   draw from the single-cell LiPo

The frequency can be increased by adjusting the three trimmer resistors
(R4, R10, R11) to reduce R_A, up to the mechanical limits of the system.
