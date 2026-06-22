# Assembly Guide — Hsiwu

This guide covers the complete assembly of the Hsiwu biomimetic bat robot
from manufactured parts to a fully functional flapping-wing system.

---

## Prerequisites

### Tools Required
- Soldering iron (temperature-controlled, fine tip)
- Solder (lead-free or 63/37 SnPb, 0.5–0.8 mm diameter)
- Solder wick / desoldering pump
- Tweezers (fine-point, anti-magnetic)
- Flux pen (no-clean)
- Digital multimeter (DMM)
- Oscilloscope (optional but recommended — Tektronix TDS1001B-SC or equivalent)
- Hex drivers: 1.5 mm, 2.0 mm
- Small Phillips screwdriver (#00, #0)
- Needle-nose pliers
- Flush cutters
- Third-hand tool / PCB vise
- Isopropyl alcohol (99%) + brush for PCB cleaning
- FDM 3D printer (if printing parts yourself)
- Digital calipers

### Materials
- Fabricated PCB (from Gerber files in `electronics/output/gerber/`)
- All components from the BOM (`BOM/PCB_Base_BOM.xlsx`)
- 3D-printed structural parts (STEP files in `mechanical/output/3dp/`)
- Laser-cut or 3D-printed rods
- M2/M3 screws and lock nuts
- 3.7V LiPo battery (single-cell)
- Motor wires (26–28 AWG stranded)
- Double-sided foam tape (for battery mounting)

---

## Step 1: PCB Assembly

### 1.1 Inspection
Before soldering, inspect the bare PCB:
- [ ] No visible shorts between adjacent traces (use magnifying glass)
- [ ] All pads are intact, no lifted pads
- [ ] Silkscreen is legible (component designators match BOM)
- [ ] Board outline matches mechanical frame contour

### 1.2 Soldering Order
Solder components from **smallest to tallest** and **lowest thermal mass first**:

1. **0805 Resistors** (R1–R11) — ceramic, robust to heat
2. **0805 Capacitors** (C1–C2, C4, C6, C8) — small MLCCs
3. **0805 LED** (LED1) — observe polarity (cathode mark)
4. **SOT-23-6** (U4, MT3608) — fine pitch, use flux
5. **SOIC-8** (U6, U7, U8 — L293D) — align pin 1 marker
6. **SOIC-14** (U5 — 74HC04) — align pin 1 marker
7. **SOP-20** (U1, U2, U3 — NE555 carriers) — use drag soldering
8. **SMC Diode** (D1) — observe polarity (cathode band)
9. **SMD Inductor** (L1) — large thermal mass, pre-heat pad
10. **SMD Electrolytics** (C3, C5, C7) — observe polarity
11. **Thru-hole Switch** (SW2) — solder from bottom side
12. **XH-2A Connectors** (U9 × 3) — ensure they sit flat
13. **Battery connector** — wire leads, strain relief

### 1.3 Soldering Tips

**For 0805 passives:**
1. Tin one pad
2. Hold component with tweezers, reflow tinned pad
3. Solder the other pad
4. Reflow the first pad for a clean joint

**For SOIC/SOP ICs:**
1. Align pin 1 (dot/notch) with silkscreen marker
2. Tack two opposite corner pins
3. Apply flux to all pins
4. Drag solder along each row
5. Remove bridges with solder wick

### 1.4 Post-Solder Inspection
- [ ] Visual inspection of all joints (shiny, concave fillet, no bridges)
- [ ] Continuity test: power rails not shorted to ground
  - DMM in continuity mode: probe between VCC and GND — should be open
- [ ] Diode test: D1 cathode → anode should be open (DMM diode mode)

---

## Step 2: PCB Power-Up Test

**Before connecting motors**, verify the power supply:

1. **Set up** — PCB on insulated surface, oscilloscope probes ready
2. **Check input** — DMM on battery terminals: 3.7–4.2 V
3. **Power on** — slide SW2 to ON
4. **Verify LED** — LED1 should illuminate green
5. **Measure rails**:
   - 5 V rail: DMM between any IC's VCC and GND → 5.0 V ± 0.25 V
   - 7.35 V rail: DMM between L293D pin 8 (VCC2) and GND → 7.35 V ± 0.5 V
6. **Check oscillation** (oscilloscope):
   - Probe NE555 output pins (U1/U2/U3 pin 3)
   - Should see ~4.8 Hz square wave, 0 V to ~5 V
   - Check all three channels
7. **Verify phase inversion** (2-channel scope):
   - Ch1: NE555 OUT
   - Ch2: 74HC04 corresponding /OUT
   - Should be complementary (one HIGH when the other is LOW)

> **If any check fails:** power off immediately and debug. Common issues:
> - No 5 V: check LDO, C2, C4
> - No 7.35 V: check MT3608 feedback resistors, L1, D1
> - No oscillation: check RC network, trimmer position
> - Wrong phase: check 74HC04 pin connections

---

## Step 3: Mechanical Assembly

### 3.1 3D-Printed Parts Checklist

| Part | Qty | Print File |
|------|-----|-----------|
| `central_support` | 1 | `mechanical/output/3dp/central_support.STEP` |
| `head_support` | 1 | `mechanical/output/3dp/head_support.STEP` |
| `tail_bracket` | 1 | `mechanical/output/3dp/tail_bracket.STEP` |
| `head_bearing_cover` | 1 | `mechanical/output/3dp/head_bearing_cover.STEP` |
| `center_bearing_cap` | 1 | `mechanical/output/3dp/center_bearing_cap.STEP` |
| `head_drive_shaft` | 1 | `mechanical/output/3dp/head_drive_shaft.STEP` |
| `head_pin` | 2 | `mechanical/output/3dp/head_pin.STEP` |
| `tail_pin` | 2 | `mechanical/output/3dp/tail_pin.STEP` |

### 3.2 Rods Checklist

| Rod | Qty (total) | Notes |
|-----|-------------|-------|
| `rod_a` | 6 | 3 per side — crank |
| `rod_bc` | 6 | 3 per side — coupler |
| `rod_d` | 6 | 3 per side — rocker |
| `rod_h` | 6 | 3 per side — auxiliary |
| `rod_ij` | 6 | 3 per side — scissor |
| `rod_kf` | 6 | 3 per side — tie |

### 3.3 Fasteners Checklist

| Fastener | Approx. Qty | Where Used |
|----------|-------------|------------|
| M2 × 10 mm CSK | ~8 | Frame to central support |
| M2 × 12 mm CSK | ~6 | Motor mounts |
| M2 × 14 mm CSK | ~4 | Bearing caps |
| M2 × 18 mm CSK | ~4 | Head/tail support through-bolts |
| M2 lock nut | ~22 | All M2 screws |
| M3 lock nut | ~4 | Frame major joints |

### 3.4 Assembly Sequence

#### 3.4.1 Frame Sub-Assembly
1. Insert bearings into `center_bearing_cap` and `head_bearing_cover`
2. Mount `head_support` to `central_support` with M2 × 10 mm screws
3. Mount `tail_bracket` to `central_support` with M2 × 10 mm screws
4. Install `lead_screw` with bearings and caps
5. Install `head_drive_shaft`
6. Insert `head_pin` and `tail_pin` into their pivot seats

#### 3.4.2 Motor Installation
1. Mount three hollow-cup motors to the motor bosses on `central_support`
   (M2 × 12 mm)
2. Attach `2-to-3_coupling` to each motor shaft
3. Verify motor shafts rotate freely by hand

#### 3.4.3 Linkage Assembly (per wing segment)
1. Attach `rod_a` (crank) to motor output → secure with set screw
2. Connect `rod_bc` (coupler) to `rod_a` with hinge pin
3. Connect `rod_d` (rocker) to `rod_bc` and to wing pivot pin
4. Attach `rod_h`, `rod_ij`, `rod_kf` for scissor linkage
5. **Repeat for all three segments per side**
6. **Mirror for the opposite wing**

#### 3.4.4 PCB Mounting
1. Place PCB onto the standoffs on `central_support`
2. Secure with M2 screws (do not overtighten — FR-4 can crack)
3. Route motor wires to the XH-2A connectors
4. Use zip ties or small adhesive clips for wire management

#### 3.4.5 Battery Installation
1. Mount the LiPo battery on the underside of `central_support`
2. Use double-sided foam tape + a velcro strap
3. Connect battery to PCB input

---

## Step 4: Final Integration Test

### 4.1 Pre-Flight Checklist
- [ ] All screws tightened (but not stripped)
- [ ] All linkages move freely through full range of motion
- [ ] No wires rubbing against moving parts
- [ ] Battery secured and connected
- [ ] PCB mounted firmly
- [ ] All XH-2A connectors fully seated

### 4.2 Power-On Sequence
1. Place robot on a soft surface (foam pad or towel)
2. Slide SW2 to ON
3. LED1 should illuminate
4. Wings should begin flapping within ~1 second
5. All three segments per wing should move in a coordinated wave

### 4.3 Tuning
- **Wing 1 (leading edge) speed** — adjust R4 trimmer
- **Wing 2 (middle) speed** — adjust R10 trimmer
- **Wing 3 (trailing edge) speed** — adjust R11 trimmer
- **Ideal pattern**: leading edge leads slightly, trailing edge follows —
  creates the bat's characteristic "wave" motion

### 4.4 Common Issues

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| One motor doesn't run | Bad solder joint on L293D or XH-2A | Reflow joints |
| Motor runs only one direction | 74HC04 channel not inverting | Check 74HC04 input connection |
| Wing binds mid-stroke | Linkage interference | Adjust rod lengths or clearances |
| Flapping too fast/slow | RC timing off | Adjust trimmer |
| No power LED | Dead battery or reversed polarity | Check battery, check D1 |
| Excessive noise | Loose screw or dry bearing | Tighten, lubricate bearing |

---

## Step 5: Maintenance

- **Bearings**: Apply a tiny drop of light machine oil every ~10 hours of
  operation
- **Linkage joints**: Inspect for wear every ~5 hours; replace rods if
  slop develops
- **PCB**: Clean with isopropyl alcohol if flux residue attracts dust
- **Battery**: Store at 3.7–3.8 V (storage charge) when not in use
- **Motors**: Brushes have finite life; expect ~50–100 hours of continuous
  operation before replacement

---

**Happy flying!** 🦇
