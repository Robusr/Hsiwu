<p align="center">
  <img src="./media/photos/project/front_view.png" alt="Hsiwu - Biomimetic Bat Robot Front Elevation" width="800"/>
</p>

<h1 align="center">Hsiwu</h1>

<p align="center">
  <strong>MCU-less Analog Biomimetic Bat Flapping-Wing Robot</strong>
  <br>
  Three-segment independently-driven wings with pure analog control
</p>

<p align="center">
  <a href="./LICENSE.md"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License: MIT"></a>
  <img src="https://img.shields.io/badge/SolidWorks-2025-e63946?logo=dassault-systemes&logoColor=white" alt="SolidWorks 2025">
  <img src="https://img.shields.io/badge/Altium_Designer-2025-00a0e3?logo=altium-designer&logoColor=white" alt="Altium Designer 2025">
  <img src="https://img.shields.io/badge/Make-FDM_3D_Print-f57c00" alt="FDM 3D Printing">
  <img src="https://img.shields.io/badge/Control-Pure_Analog-f0c040" alt="Pure Analog Control">
  <br>
  <a href="./README.zh-CN.md">中文版本</a> | <a href="./README.md">English</a>
</p>

---

## Name Origin

<p align="center">
  <img src="./media/photos/hsiwu/hsiwu.png" alt="Hsi Wu — Sky Demon from Jackie Chan Adventures" width="300"/>
  <br>
  <em><strong>Hsi Wu</strong> (西木 / Xī Mù), the Sky Demon of <strong>Jackie Chan Adventures</strong>.</em>
</p>

The project name **Hsiwu** is derived from **Hsi Wu** (西木), the bat-like **Sky Demon** from *Jackie Chan Adventures* (《成龙历险记》). A fitting namesake: just as Hsi Wu ruled the sky with his segmented, morphing wings, this robot replicates the segmented membrane deformation of a real bat in flight.

---

## About The Project

**Hsiwu** — named after the bat-like Sky Demon — is a biologically inspired bionic bat flapping-wing robot. Unlike conventional single-segment rigid-wing drones, it replicates the segmented membrane deformation of a real bat in flight. Each wing is divided into three independently driven segments — **leading edge**, **middle section**, and **trailing edge** — each powered by its own DC motor.

The bionic design takes the *Rhinolophus luctus* (great woolly horseshoe bat) as its morphological reference. Through a crank-rocker four-bar linkage mechanism, the continuous rotary motion of each DC motor is converted into the oscillating reciprocating swing that drives the wing segments—faithfully reproducing the coupled flapping-and-stretching kinematics of a real bat wing membrane.

A defining characteristic of this project is its **pure analog control architecture**. The system uses no microcontroller (MCU) and no embedded software whatsoever. Instead, it relies entirely on discrete analog and digital logic circuits — **NE555** timers, **74HC04** inverters, and **L293D** H-bridge drivers — to generate the synchronized periodic reciprocating motion that drives the wings.

The project spans a complete engineering stack: analog electronics design, digital logic, custom PCB layout, mechanical structure modeling (SolidWorks), FDM 3D printing, and hardware assembly and debugging.

> **Reference Paper**: The aerodynamic and mechanical principles are adapted from *"Design and Manufacture of Bionic Bat Aircraft"* (Qi Jinhao, Zhang Weiping, 2020), published in *Machine Design and Research*, Vol. 36, No. 5. The reference prototype achieves a 205 mm wingspan, 17.81 g total mass, 70° flapping angle, ~10 Hz flapping frequency, and ~0.16 N lift under a 3.7 V power supply.

<p align="center">
  <img src="./media/photos/project/colored_version.png" alt="Hsiwu Full Assembly Render" width="700"/>
  <br>
  <em><strong>Figure 0 — Project Overview.</strong> Full-color 3D render of the complete Hsiwu assembly. The bilateral symmetric frame chassis (central support, head support, tail bracket), six independently-driven wing segments (three per side — leading, middle, trailing edges), linkage rods, lead-screw-driven scissor expansion mechanism, and custom-shaped PCB are all visible. This render corresponds to the final assembly state defined in <code>final_assembly.SLDASM</code>.</em>
</p>

---

## Key Features

- **Bio-inspired Wing Kinematics** — Three independently-driven segments per wing (Leading, Middle, Trailing), each with its own DC motor, mimicking the stretching and bending of a real bat's wing membrane. The crank-rocker four-bar linkage converts continuous motor rotation into oscillatory flapping.
- **Zero MCU / Zero Code** — Pure analog control: NE555 astable multivibrators for timing, 74HC04 hex inverters for phase splitting, and L293D dual H-bridges for bidirectional motor drive. No microcontroller, no firmware, no programming required.
- **Custom Shaped PCB** — A specialized irregularly-shaped PCB designed in EasyEDA (LCEDA) and Altium Designer, contour-fit to the internal mechanical frame. Integrates power management, signal generation, and motor driving onto a single compact board.
- **Modular Mechanical Design** — Fully parameterized 3D models built in SolidWorks. Frame components are FDM 3D-printable, assembled with standard M2/M3 screws and hinge linkages for easy tuning and repair.
- **Integrated Power Management** — Single-cell 3.7V LiPo battery input with MT3608 boost converter (3.7V → 7.35V for motors), on-board LDO regulation (5V for logic), reverse-polarity protection, and power switch with indicator LED.

---

## System Architecture

### 1. Electronics System (`/electronics`)

The core logic is distributed across five functional blocks integrated onto a single custom PCB:

<p align="center">
  <img src="./media/photos/project/PCB_Base_doc.png" alt="Full System Schematic" width="800"/>
  <br>
  <em><strong>Figure 1 — Full System Schematic.</strong> Complete circuit diagram showing four functional blocks: (left) MT3608 boost converter with Schottky diode reverse-polarity protection and power switch; (upper-middle) three NE555 astable multivibrator stages generating independent ~4.8 Hz square-wave timing signals; (right) 74HC04 hex inverter for complementary phase generation; (lower) three L293D dual H-bridge drivers, each using one half-bridge channel per motor to produce continuous forward/reverse reciprocation.</em>
</p>

| Block | Components | Function |
|-------|-----------|----------|
| **Power Supply** | MT3608, LDO, Schottky diode | 3.7V LiPo → 7.35V (boost, motors) + 5V (LDO, logic). Reverse-polarity protection, on/off switch with indicator LED. |
| **Timing Generation** | 3× NE555 (astable) | Three independent, synchronized square-wave oscillators at ~4.8 Hz. Sets the flapping frequency. Each NE555's RC network can be independently tuned for per-segment timing adjustment. |
| **Phase Inversion** | 1× 74HC04 hex inverter | Inverts the three NE555 outputs to produce complementary logic levels for H-bridge direction control. Each NE555 output drives one inverter channel, yielding the inverted signal for the opposing half-bridge. |
| **Motor Drive** | 3× L293D dual H-bridge | Each L293D uses one half-bridge channel per motor. Complementary logic signals produce continuous forward/reverse reciprocation. Built-in flyback diodes protect against inductive kickback. |
| **PCB Layout** | Custom contour | Vertical modular layout; board outline shaped to fit the internal mechanical frame. Irregular board profile matches the central support chassis curvature. |

<p align="center">
  <img src="./media/photos/project/PCB_3d_top.png" alt="3D PCB Render" width="400"/>
  <img src="./media/photos/project/PCB_top.png" alt="2D Top Layer" width="400"/>
  <br>
  <em><strong>Figure 2a–2b — PCB Design Views (I).</strong> Left: 3D render showing component placement on the custom-shaped PCB — three NE555 (SOP-20, U1–U3), 74HC04 (SOIC-14, U5), three L293D (SOIC-8, U6–U8), MT3608 (SOT-23-6, U4), SMC Schottky diode (D1), SMD inductor (L1), XH-2A connectors (U9), 0805 passive components (R1–R11, C1–C8), and slide switch (SW2). Right: 2D top-layer copper routing with silkscreen overlay.</em>
</p>

<p align="center">
  <img src="./media/photos/project/PCB_bottom.png" alt="2D Bottom Layer" width="400"/>
  <img src="./media/photos/project/PCB_raw_top.png" alt="Raw Top Routing" width="400"/>
  <img src="./media/photos/project/PCB_raw_bottom.png" alt="Raw Bottom Routing" width="400"/>
  <br>
  <em><strong>Figure 2c–2e — PCB Design Views (II).</strong> Left to right: Bottom copper layer with silkscreen; raw top-layer routing trace (no fill); raw bottom-layer routing trace (no fill). The Gerber fabrication files are available in <code>electronics/output/gerber/</code>.</em>
</p>

### 2. Mechanical Structure (`/mechanical`)

The mechanical assembly is designed in SolidWorks (`.SLDPRT` / `.SLDASM` source files) and manufactured via FDM 3D printing. The kinematic principles are adapted from the reference paper *"Design and Manufacture of Bionic Bat Aircraft"* (Qi & Zhang, 2020).

#### 2.1 Kinematic Mechanisms (Reference Paper)

The following kinematic schematics are reproduced from the reference paper. They illustrate the four core mechanisms that underlie the wing drive train:

<p align="center">
  <img src="./media/photos/reference/figure1.png" alt="Crank-Rocker Mechanism" width="400"/>
  <img src="./media/photos/reference/figure2.png" alt="Linkage Dimensions" width="400"/>
  <br>
  <em><strong>Figure 3a–3b — Crank-Rocker Four-Bar Linkage.</strong> Left: Kinematic diagram of the crank-rocker mechanism that converts the motor shaft's continuous rotary motion into the rocker arm's oscillating up/down swing. The crank (driven by the geared hollow-cup DC motor) rotates continuously; the connecting rod transmits motion to the rocker, whose output angle determines the wing segment's flapping amplitude (~70° in the reference design). Right: Dimensioned linkage drawing with parameter table — key link lengths a/b/c/d/e/f/g/h/i/j/k and crank/rocker pivot angles α/β are dimensioned for the target 75° flapping stroke. The reference prototype uses linkage ratios optimized for a 205 mm wingspan.</em>
</p>

<p align="center">
  <img src="./media/photos/reference/figure3.png" alt="Scissor Mechanism" width="400"/>
  <img src="./media/photos/reference/figure4.png" alt="Leg Mechanism" width="400"/>
  <br>
  <em><strong>Figure 3c–3d — Scissor Expansion & Leg Drive Mechanism.</strong> Left: Scissor mechanism controlled by a lead screw (P = 0.4 mm pitch, d₂ = 1.74 mm effective diameter). The motor-driven lead screw advances/retracts a traveling nut, which pushes/pulls a scissor linkage to control wing membrane expansion and contraction. The reference paper calculates a lead screw driving torque M = 0.0064 N·mm. Right: Leg/transmission mechanism showing the motor-to-linkage coupling geometry, with pivot points O–O′ and reaction forces F<sub>A</sub>, F<sub>B</sub> resolved through static equilibrium analysis. The overall mechanical advantage is designed to deliver ~0.16 N of lift from a 3.7 V supply.</em>
</p>

#### 2.2 Project Mechanical Assembly

The project's mechanical assembly consists of the following sub-assemblies (all SolidWorks source files in `mechanical/source/`):

| Sub-assembly | Path | Key Parts | Description |
|-------------|------|-----------|-------------|
| **Structural Frame** | `structural/` | `central_support`, `head_support`, `tail_bracket` | Left-right symmetric chassis; the central support forms the main body, with head/tail brackets anchoring the wing pivot points |
| **Rod Linkages** | `rods/` | `rod_a`, `rod_bc`, `rod_d`, `rod_h`, `rod_ij`, `rod_kf` | Six linkage rod types forming the four-bar crank-rocker kinematic chains for each wing segment |
| **Motor & Coupling** | `motor/` | `four-stage_geared_hollow_cup_motor`, `2-to-3_coupling` | Hollow-cup DC motor with 4-stage planetary gear reduction; 2-to-3 shaft coupler for connecting to the lead screw |
| **Lead Screw Assembly** | `structural/` | `lead_screw`, `head_drive_shaft`, `bearing`, `center_bearing_cap`, `head_bearing_cover` | Lead screw (P = 0.4 mm) drives the scissor expansion linkage; supported by miniature bearings at both ends |
| **Pivot Hardware** | `structural/` | `head_pin`, `tail_pin` | Precision hinge pins for wing segment articulation at the leading and trailing pivot points |
| **Fasteners** | `fasteners/` | `SCA_M2_L10/L12/L14/L18`, `SLMNA-M2`, `SLMNA-M3` | M2 countersunk screws (10–18 mm lengths), M2/M3 lock nuts for frame assembly |
| **Final Assembly** | `assembly/` | `final_assembly.SLDASM` | Master assembly file referencing all sub-components in their assembled positions |

<p align="center">
  <img src="./media/photos/project/screw_movement.gif" alt="Lead Screw Animation" width="600"/>
  <br>
  <em><strong>Figure 4 — Lead Screw Transmission Animation.</strong> Dynamic demonstration of the lead-screw-driven scissor mechanism. As the motor rotates the lead screw, the traveling nut advances linearly, pushing the scissor linkage open to expand the wing membrane area — or retracting to contract it. This coupled expansion/flapping motion is a key biomimetic feature that replicates the bat wing's variable-camber aerodynamics.</em>
</p>

<p align="center">
  <img src="./media/photos/project/top_view.png" alt="Top View" width="400"/>
  <img src="./media/photos/project/line_drawing.png" alt="Isometric Wireframe" width="400"/>
  <br>
  <em><strong>Figure 5a–5b — Mechanical Drawings.</strong> Left: Top/plan view — reveals the motor placement, linkage rod routing, and overall wingspan geometry. Right: Isometric wireframe view — a line-drawing render showing the complete kinematic skeleton with all linkage pivot points, rod connections, and structural frame members visible in a single perspective. The front elevation view is shown in the project banner above.</em>
</p>

---

## Repository Structure

```
HSIWU/
├── electronics/                 # PCB design files
│   ├── source/                  #   LCEDA project (.eprj2) + Altium Designer source files (.schdoc, .pcbdoc)
│   └── output/                  #   Gerber files, BOM, pick-and-place (.csv/.xlsx), PDF schematic
├── mechanical/                  # 3D CAD models (SolidWorks)
│   ├── source/                  #   Native SLDPRT/SLDASM files
│   │   ├── structural/          #     Frame, lead screw, bearings, pins, brackets
│   │   ├── rods/                #     Six linkage rod types (a, bc, d, h, ij, kf)
│   │   ├── motor/               #     Geared hollow-cup motor + shaft coupler
│   │   ├── fasteners/           #     M2/M3 screws & lock nuts
│   │   ├── pcb_3d_models/       #     PCB with all component 3D models placed
│   │   └── assembly/            #     Master assembly file
│   └── output/                  #   Export files — STEP (3D printing), STL (laser cutting)
├── docs/                        # Technical documentation
│   ├── en/                      #   English docs — theory of operation, mechanical design, assembly guide
│   └── zh-CN/                   #   中文文档 — 工作原理、机械设计、组装指南
├── BOM/                         # Bill of Materials spreadsheet (.xlsx)
├── media/                       # Project media assets
│   ├── photos/
│   │   ├── project/             #   Renders, PCB views, mechanical drawings, test photos
│   │   └── reference/           #   Reference paper figures and the original paper (PDF)
│   └── videos/                  #   Demonstration videos (.mp4)
├── .github/                     # GitHub community files
│   ├── CODEOWNERS               #   Code ownership definitions
│   ├── PULL_REQUEST_TEMPLATE.md #   PR template
│   ├── ISSUE_TEMPLATE/          #   Bug report & feature request templates
│   └── FUNDING.yml              #   Funding/sponsor configuration
├── .gitignore                   # CAD/EDA/3D-printing-aware ignore rules
├── .gitattributes               # Binary/text handling & diff configuration
├── CHANGELOG.md                 # Version history (Keep a Changelog format)
├── CONTRIBUTING.md              # Contributor guidelines (English)
├── CONTRIBUTING.zh-CN.md        #   中文贡献者指南
├── LICENSE.md                   # MIT License
├── README.md                    # Project overview (English)
└── README.zh-CN.md              # 项目概览（中文）
```

---

## Bill of Materials

See [`BOM/`](BOM/) for the complete component spreadsheet. Key components:

| Category | Item | Qty | Notes |
|----------|------|-----|-------|
| **Frame** | 3D-printed parts (ABS/PLA) | — | Central support, head support, tail bracket, bearing caps; 0.2 mm layer height recommended |
| **Fasteners** | M2 countersunk screws (10/12/14/18 mm) | — | SCA series |
| | M2/M3 lock nuts | — | SLMNA series |
| **Bearings** | Miniature ball bearings | — | For lead screw and pivot joints |
| **Motors** | DC hollow-cup geared motors | 3 | Four-stage planetary gear reduction; one per wing segment |
| **Power** | 3.7V LiPo battery | 1 | Single-cell |
| **ICs** | NE555 timer (SOIC-8 equiv., on SOP-20 carrier) | 3 | Astable multivibrator configuration |
| | 74HC04 hex inverter (SOIC-14) | 1 | Six NOT gates; three used for phase inversion |
| | L293D dual H-bridge driver (SOIC-8 equiv.) | 3 | One half-bridge channel per motor |
| | MT3608 boost converter (SOT-23-6) | 1 | 3.7V → 7.35V step-up |
| **Passives** | Resistors, capacitors (0805) | — | Timing RC networks, decoupling, filter |
| | Schottky diode (SMC) | 1 | Reverse-polarity protection |
| | Power inductor (SMD, 7.3×6.8 mm) | 1 | MT3608 boost inductor |
| | Adjustable resistors (SMD trimmer) | 3 | Per-segment frequency fine-tuning |
| **Connectors** | XH-2A 2-pin header (P2.50) | — | Motor wire connections |
| | Slide switch (SS12D07VG4) | 1 | Power on/off |
| | Indicator LED (0805) | 1 | Green power-on indicator |

---

## How to Replicate

### 1. Electronics
Open [`electronics/`](electronics/). The schematic is documented in `PCB_Base.pdf` and `PCB_Base_doc.png` (see **Figure 1**). The board can be manufactured directly from the Gerber files in `electronics/output/gerber/`, or you can re-export from the EasyEDA (LCEDA) project in `electronics/source/JLC/`. The pick-and-place file (`electronics/output/pick_and_place/PCB_BASE.csv`) and BOM (`electronics/output/bom/PCB_Base_BOM.xlsx`) are ready for SMT assembly services.

### 2. Mechanical
Find the 3D models in [`mechanical/`](mechanical/). Export the parts to STL and print them on a standard FDM 3D printer (0.2 mm layer height recommended; ABS for structural durability or PLA for ease of printing). The STEP files in `mechanical/output/3dp/` are ready for direct slicing and printing. For laser-cut parts (rods), use the STL files in `mechanical/output/laser/`. To modify the design, open the native SolidWorks source files in `mechanical/source/` — all parts are fully parameterized.

### 3. Assembly
1. Solder all components onto the custom PCB according to the schematic (**Figure 1**). Refer to the PCB layout views (**Figure 2a–2e**) for component placement.
2. Connect the three motor wires to the XH-2A headers on the PCB.
3. 3D-print the structural frame parts: `central_support`, `head_support`, `tail_bracket`, `head_bearing_cover`, `center_bearing_cap`, `head_drive_shaft`, `head_pin`, `tail_pin`.
4. 3D-print or laser-cut the linkage rods: `rod_a`, `rod_bc`, `rod_d`, `rod_h`, `rod_ij`, `rod_kf`.
5. Assemble the frame using M2/M3 screws, lock nuts, and miniature bearings as specified in `final_assembly.SLDASM`. Refer to the mechanical drawings (**Figure 5a–5b**) and the front elevation banner for assembly reference.
6. Mount the PCB onto the central support chassis.

### 4. Power Up
Insert the 3.7V LiPo battery and flip the power switch (SW2). The onboard green LED (LED1) should illuminate, and the wings should begin flapping at the designed ~4.8 Hz frequency. The flapping rate can be fine-tuned by adjusting the three trimmer resistors (R4, R10, R11) that set each NE555's RC time constant.

---

## Demo & Testing

During the powered-on test, the onboard green indicator LED illuminates and the 3D-printed robot executes the full flapping motion. A Tektronix TDS1001B-SC oscilloscope is used to monitor the ~4.8 Hz square-wave output from the NE555 oscillator stages and verify signal integrity.

<p align="center">
  <img src="./media/photos/project/test1.JPG" alt="Assembly Setup" width="400"/>
  <img src="./media/photos/project/test3.JPG" alt="PCB Soldering Setup" width="400"/>
  <br>
  <em><strong>Figure 6a–6b — Assembly & Fabrication.</strong> Left: Bench assembly setup — third-hand tool with magnifying glass holding the partially assembled mechanical frame; PCB, motors, and linkage rods visible on the work mat. Right: PCB soldering station — the custom-shaped board undergoing manual SMT soldering of 0805 passives and ICs.</em>
</p>

<p align="center">
  <img src="./media/photos/project/test2.JPG" alt="Live Oscilloscope Test" width="600"/>
  <br>
  <em><strong>Figure 7 — Live Oscilloscope Test.</strong> Tektronix TDS1001B-SC digital storage oscilloscope probing the NE555 output waveform during a powered-on test. The scope displays the ~4.8 Hz square wave (period ≈ 208 ms) with voltage swing from 0 V to V<sub>CC</sub> (5 V logic rail), confirming correct astable multivibrator operation. Multiple channels allow simultaneous monitoring of all three motor drive signals for phase verification.</em>
</p>

---

## Design Specifications

| Parameter | Value | Source |
|-----------|-------|--------|
| **Bionic Reference** | *Rhinolophus luctus* (great woolly horseshoe bat) | Reference paper |
| **Wingspan** | 205 mm (reference) | Qi & Zhang, 2020 |
| **Total Mass** | 17.81 g (reference) | Qi & Zhang, 2020 |
| **Flapping Frequency** | ~4.8 Hz (this project) / ~10 Hz (reference) | NE555 RC network / paper |
| **Flapping Angle** | ~70° (reference target) | Crank-rocker geometry |
| **Power Supply** | 3.7V LiPo single-cell | This project |
| **Motor Supply** | 7.35V (boosted) | MT3608 converter |
| **Logic Supply** | 5V (regulated) | On-board LDO |
| **Motor Type** | Hollow-cup DC, 4-stage planetary geared | ZWPD006006-26 (reference) |
| **Lead Screw Pitch** | P = 0.4 mm, d₂ = 1.74 mm | Reference paper |
| **Gear Ratio** | 1:4 (motor output : lead screw) | Reference paper |
| **PCB Design Tools** | EasyEDA (LCEDA) + Altium Designer | This project |
| **CAD Software** | SolidWorks (native .SLDPRT/.SLDASM) | This project |
| **Manufacturing** | FDM 3D printing (frame) + FR-4 PCB | This project |

---

## License

Distributed under the [MIT License](LICENSE.md).

---

## Acknowledgments

- **Reference Work** — *"Design and Manufacture of Bionic Bat Aircraft"* (Qi Jinhao, Zhang Weiping, 2020), published in *Machine Design and Research* (Vol. 36, No. 5, pp. 38–43), Shanghai Jiao Tong University. This paper provides the theoretical foundation for bat flight kinematics, four-bar linkage design, scissor mechanism force analysis, and prototype validation methodology upon which this project's mechanical design is based.
- **Name Inspiration** — The character **Hsi Wu** (西木, the Sky Demon) from the animated series *Jackie Chan Adventures* (《成龙历险记》), whose iconic bat-like form inspired the project's name and its tribute to segmented-wing biomechanics.
- **Tools** — SolidWorks (mechanical CAD), EasyEDA / LCEDA (PCB design), Altium Designer (schematic capture), Tektronix TDS1001B-SC (test & measurement).
- **Inspiration** — FESTO BionicOpter and Bat Bot B2 (Caltech/UIUC) for demonstrating the feasibility of bio-inspired bat flight; the broader bat flight biomechanics literature (Swartz et al., Tian et al., Wolf et al.) for wing membrane mechanics and flapping kinematics research.