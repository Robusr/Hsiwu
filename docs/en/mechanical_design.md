# Mechanical Design — Hsiwu

## Design Philosophy

The Hsiwu mechanical structure is designed around three principles:

1. **Biomimetic fidelity** — the segmented wing kinematics must faithfully
   reproduce the coupled flapping-and-stretching motion of a real bat wing
   membrane (*Rhinolophus luctus* as the morphological reference).
2. **Manufacturability** — all structural components are FDM 3D-printable
   without supports on a standard consumer printer.
3. **Modularity** — sub-assemblies are independent; a damaged wing segment or
   linkage can be replaced without reprinting the entire robot.

## Kinematic Foundation

### Crank-Rocker Four-Bar Linkage

The core mechanism that converts continuous motor rotation into oscillating
wing flapping is the **crank-rocker four-bar linkage** (Grashof type: crank-rocker).

```
                         connecting rod (rod_bc)
                    ┌─────────────────────────────┐
                    │                             │
    crank (rod_a) ──┤                             ├── rocker (rod_d)
    (fixed to motor │                             │   (output to wing
     shaft, rotates │                             │    segment)
     360° CW)       │                             │
                    │      ground link (frame)     │
                    └─────────────────────────────┘
```

**Grashof condition:** For a crank-rocker to exist, the sum of the shortest
and longest links must be less than the sum of the remaining two links:
```
s + l < p + q
```
where s = shortest link, l = longest link, and p, q are the other two.

In the Hsiwu linkage:
- **Crank** (rod_a) = shortest link — driven by motor, rotates continuously
- **Coupler** (rod_bc) = intermediate — transmits motion to rocker
- **Rocker** (rod_d) = output — oscillates through ~70°
- **Ground** = the mechanical frame (distance between motor shaft and rocker pivot)

### Flapping Angle

The rocker angular range (flapping angle) is determined by the linkage
geometry:
```
θ_max = arcsin((r_crank + r_coupler) / r_rocker)  [approximate upper bound]
θ_min = arcsin((r_crank - r_coupler) / r_rocker)  [approximate lower bound]
Flapping amplitude = θ_max − θ_min ≈ 70°
```

### Six Linkage Rod Types

Each wing segment (3 per side) uses its own set of linkages. All three share
the same kinematic topology but may differ slightly in dimensions to account
for the wing's tapered planform.

| Rod | Role | Notes |
|-----|------|-------|
| `rod_a` | Crank | Attaches to motor output shaft; shortest link |
| `rod_bc` | Coupler | Connects crank to rocker; the BC designation indicates two variants sharing a common geometry |
| `rod_d` | Rocker | Output link; drives the wing segment |
| `rod_h` | Auxiliary link | Connects to the lead screw driven nut |
| `rod_ij` | Scissor link | Part of the scissor expansion mechanism |
| `rod_kf` | Tie link | Structural connector between wing segments |

## Lead Screw & Scissor Mechanism

### Purpose

In a real bat, the wing membrane not only flaps but **stretches** — the
cambers changes dynamically across the wing cycle. Hsiwu replicates this
with a **lead screw-driven scissor linkage** that expands and contracts
the effective wing area in coordination with the flapping motion.

### Mechanism

```
   motor → 2-to-3 coupling → lead screw (rotates)
                                      │
                 traveling nut (translates linearly)
                                      │
                           scissor linkage (expands/contracts)
                                      │
                         wing membrane area changes
```

### Lead Screw Specifications

| Parameter | Value |
|-----------|-------|
| Pitch (P) | 0.4 mm |
| Effective diameter (d₂) | 1.74 mm |
| Driving torque (M) | 0.0064 N·mm (calculated) |
| Gear ratio (motor : screw) | 1:4 |

### Torque Calculation

From the reference paper (Qi & Zhang, 2020), the lead screw driving torque is:
```
M = F × d₂/2 × tan(α + ρ)
```
where:
- F = axial load on the nut
- d₂ = effective diameter
- α = lead angle = arctan(P / (π·d₂))
- ρ = friction angle = arctan(μ)

With P = 0.4 mm, d₂ = 1.74 mm, μ ≈ 0.1 (steel-on-plastic):
```
α = arctan(0.4 / (π × 1.74)) ≈ 4.2°
ρ = arctan(0.1) ≈ 5.7°
M = 0.0064 N·mm
```

### Force Analysis

The scissor mechanism trades displacement for force. As the nut advances by
Δx, the scissor tips move laterally by Δy. The mechanical advantage is:
```
MA = F_y / F_x ≈ cot(β) / 2
```
where β is the scissor half-angle. As the scissor opens (β → small), MA is
high; as it nears full extension (β → large), MA decreases.

## Structural Frame

### Central Support (`central_support`)

The main chassis. Features:
- Motor mounting bosses (three per side)
- Bearing seats for the lead screw
- PCB mounting standoffs (contour-matched)
- M2/M3 clearance holes for frame assembly
- Weight-reduction cutouts

### Head & Tail Supports

- **Head support** — anchors the leading-edge wing pivot and the head bearing
  of the lead screw
- **Tail bracket** — anchors the trailing-edge pivot point

### Bearings & Pins

- **Bearings** — miniature ball bearings support the lead screw at both ends,
  housed in `center_bearing_cap` and `head_bearing_cover`
- **Head pin / Tail pin** — precision hinge pins for wing segment articulation

## Motor & Coupling

### Hollow-Cup DC Motor

The reference motor type is ZWPD006006-26, a hollow-cup (coreless) DC motor
with a 4-stage planetary gearbox.

**Hollow-cup motor advantages:**
- Lower rotor inertia → faster response to direction reversals
- No cogging torque → smooth operation at low speeds
- Higher efficiency → longer battery life

**4-stage planetary gearbox:**
- High reduction ratio in a compact form factor
- Coaxial input/output → simplifies mechanical layout
- Even torque distribution across planet gears → durability

### 2-to-3 Shaft Coupler

Connects the motor output shaft (2 mm diameter) to the lead screw input
(3 mm diameter). The coupler accommodates minor misalignment and transmits
torque via set screws.

## Assembly Structure

```
final_assembly.SLDASM
├── central_support.SLDPRT                 # Main chassis
├── head_support.SLDPRT                    # Forward frame member
├── tail_bracket.SLDPRT                    # Aft frame member
├── [Left Wing]
│   ├── rod_a.SLDPRT × 3                   # Cranks (one per segment)
│   ├── rod_bc.SLDPRT × 3                  # Couplers
│   ├── rod_d.SLDPRT × 3                   # Rockers
│   ├── rod_h.SLDPRT × 3                   # Auxiliary links
│   ├── rod_ij.SLDPRT × 3                  # Scissor links
│   ├── rod_kf.SLDPRT × 3                  # Tie links
│   └── motor.SLDASM × 3                   # Motor assemblies
├── [Right Wing — mirror of left]
├── lead_screw.SLDPRT                      # Lead screw shaft
├── head_drive_shaft.SLDPRT                # Drive shaft
├── bearing.SLDPRT × 2                     # Bearings
├── center_bearing_cap.SLDPRT              # Bearing retainer
├── head_bearing_cover.SLDPRT              # Bearing cover
├── head_pin.SLDPRT                        # Leading edge pivot
├── tail_pin.SLDPRT                        # Trailing edge pivot
└── [Fasteners]
    ├── SCA_M2_L10.SLDPRT                  # M2 × 10mm countersunk
    ├── SCA_M2_L12.SLDPRT                  # M2 × 12mm
    ├── SCA_M2_L14.SLDPRT                  # M2 × 14mm
    ├── SCA_M2_L18.SLDPRT                  # M2 × 18mm
    ├── SLMNA-M2.SLDPRT                    # M2 lock nut
    └── SLMNA-M3.SLDPRT                    # M3 lock nut
```

## Manufacturing Notes

### FDM 3D Printing

| Setting | Value |
|---------|-------|
| Layer height | 0.2 mm |
| Nozzle diameter | 0.4 mm |
| Infill | 20–30% (gyroid or grid) |
| Perimeters | 3 |
| Supports | None required |
| Material | ABS (durability) or PLA+ (ease) |

### Laser Cutting (Rods)

For rods, laser-cut acrylic or plywood can be used as an alternative to 3D
printing, providing better dimensional accuracy:
- 2–3 mm thick acrylic sheet
- STL files in `mechanical/output/laser/`
- Post-cut: debur edges, test-fit bearings

### Assembly Tips

1. **Bearings first** — press miniature bearings into their seats before
   assembling the frame
2. **Dry-fit** — assemble without screws first to verify all clearances
3. **Thread locking** — use a tiny drop of thread-locker (low-strength,
   e.g., Loctite 222) on motor mount screws
4. **Linkage alignment** — verify that all four-bar linkages move freely
   through their full range before connecting motors
5. **Wire routing** — run motor wires along the frame's internal channels
   to the PCB XH-2A connectors
