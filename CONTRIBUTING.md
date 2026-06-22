# Contributing to Hsiwu

<p align="right">
  <a href="./CONTRIBUTING.zh-CN.md">中文版本</a>
</p>

Thank you for your interest in contributing to **Hsiwu**, the MCU-less analog
biomimetic bat flapping-wing robot!

This is a **hardware-first** open-source project. Contributions can take many
forms — design improvements, documentation fixes, community support, or
showcasing your own Hsiwu build. Below are the guidelines for each type of
contribution.

---

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Ways to Contribute](#ways-to-contribute)
- [Project Architecture Overview](#project-architecture-overview)
- [Development Workflow](#development-workflow)
  - [Branching](#branching)
  - [Commit Messages](#commit-messages)
  - [Pull Requests](#pull-requests)
- [Domain-Specific Guidelines](#domain-specific-guidelines)
  - [Electronics (PCB)](#electronics-pcb)
  - [Mechanical (CAD)](#mechanical-cad)
  - [Documentation](#documentation)
- [Testing & Verification](#testing--verification)
- [Reporting Bugs](#reporting-bugs)
- [Feature Requests](#feature-requests)
- [Questions](#questions)

---

## Code of Conduct

This project and everyone participating in it is governed by a simple principle:
**be respectful and constructive**. We welcome contributors from all backgrounds
and skill levels. Harassment, trolling, and spam will not be tolerated.

---

## Ways to Contribute

| What | How | Best For |
|------|-----|----------|
| **Build it yourself** | Follow the [How to Replicate](README.md#how-to-replicate) guide and share your experience | Everyone! We'd love to see your build photos |
| **Improve the design** | Propose changes to the schematic, PCB layout, or mechanical design | Engineers with EDA/CAD experience |
| **Fix or improve documentation** | Edit README, docs/, or inline comments | Writers, translators, detail-oriented folks |
| **Report bugs** | Open a GitHub Issue using the Bug Report template | Everyone |
| **Suggest features** | Open a GitHub Issue using the Feature Request template | Everyone |
| **Translate** | Help localize docs into additional languages | Multilingual contributors |
| **Share resources** | Add to references, tutorials, or related projects | Researchers, makers |

---

## Project Architecture Overview

Before contributing, understand the system:

```
3.7V LiPo → [Reverse-Polarity Protection] → MT3608 Boost → 7.35V (motor rail)
                                          → LDO → 5V (logic rail)

5V logic rail:
  NE555-1 (astable, ~4.8 Hz) →  OUT1 → 74HC04 → /OUT1  → L293D-1 → Motor 1 (Leading Edge)
  NE555-2 (astable, ~4.8 Hz) →  OUT2 → 74HC04 → /OUT2  → L293D-2 → Motor 2 (Middle)
  NE555-3 (astable, ~4.8 Hz) →  OUT3 → 74HC04 → /OUT3  → L293D-3 → Motor 3 (Trailing Edge)

  Each motor drives a crank-rocker four-bar linkage that oscillates
  the corresponding wing segment through ~70° at ~4.8 Hz.
```

Key constraint: **no MCU, no firmware, no programmable logic**. All control is
discrete analog/digital. Contributions should respect this design philosophy.

---

## Development Workflow

### Branching

1. **Fork** the repository.
2. Create a feature branch from `main`:
   ```
   git checkout -b feature/your-feature-name
   ```
   Branch naming prefixes:
   - `feature/` — new capability or design change
   - `fix/` — bug fix (e.g., wrong resistor value, dimension error)
   - `docs/` — documentation-only changes
   - `media/` — adding or updating images/videos/renders

### Commit Messages

Follow this format:
```
<area>: <imperative summary>

[Optional detailed body explaining why, not just what.]
```

Examples:
- `pcb: increase MT3608 output capacitor to 22µF`
- `mech: add fillet to central_support motor mount`
- `docs: fix pin numbering in replication guide`
- `media: add oscilloscope screenshot for 7.35V rail`

### Pull Requests

1. **Keep PRs focused.** One logical change per PR.
2. **Link to an Issue** where applicable.
3. **For design changes**: include before/after screenshots, Gerber diffs, or
   STEP comparison renders.
4. **For documentation**: verify all internal links (`[text](path)`) are valid.
5. **Wait for review.** A maintainer will review your PR. For mechanical/PCB
   changes, review may take longer because it requires opening CAD/EDA files.

---

## Domain-Specific Guidelines

### Electronics (PCB)

**Tools accepted:**
- EasyEDA / LCEDA (`.eprj2`) — primary design tool
- Altium Designer (`.schdoc`, `.pcbdoc`) — secondary, for re-capture

**When modifying the PCB:**

1. **Never break the board contour** — it must remain compatible with the
   mechanical frame. The board outline matches `central_support` geometry.
2. **Schematic changes first** — update the schematic and re-annotate before
   pushing to PCB layout.
3. **Re-export all outputs** after layout changes:
   - Gerber files → `electronics/output/gerber/`
   - Pick-and-place → `electronics/output/pick_and_place/`
   - BOM → `electronics/output/bom/`
4. **Motor connector footprint** — XH-2A (P2.50 mm pitch). Do not change
   without coordinating with the mechanical CAD (wire routing).
5. **Power section isolation** — keep the boost converter loop (MT3608 + L1 +
   D1 + output cap) as tight as possible. Do not run high-current traces under
   the NE555 timing section.

**Schematic change checklist:**
- [ ] Component values included (R, C, L with tolerances)
- [ ] Power ratings checked (esp. boost inductor saturation current)
- [ ] Timing verified (f = 1.44 / ((R_A + 2R_B) × C) for NE555 astable)
- [ ] New Gerbers generated
- [ ] BOM updated with supplier part numbers

### Mechanical (CAD)

**Tool:** SolidWorks 2025 (`.SLDPRT`, `.SLDASM`). If using a different
SolidWorks version, note it in your PR.

**When modifying mechanical parts:**

1. **Keep parameters in the model** — use equations and design tables rather
   than hard-coded dimensions.
2. **Export updated STEP/STL** for parts you change:
   - 3D-printable parts → `mechanical/output/3dp/` (STEP format)
   - Laser-cut parts → `mechanical/output/laser/` (STL format)
3. **Mating references** — do not change existing mate faces/axes/points in the
   assembly without documenting the required changes.
4. **Fastener compatibility** — use only M2/M3 fasteners as already specified.
   Do not introduce new screw sizes without discussion.

**3D printing notes:**
- 0.2 mm layer height (standard)
- 0.4 mm nozzle (standard)
- No supports required for any current part (designed for printability)
- Verification: print and test-fit at least one copy before submitting the PR

### Documentation

- **English** is the primary language. Chinese (zh-CN) translations are
  maintained in parallel files under `docs/zh-CN/` and `README.zh-CN.md`.
- **Markdown** with GitHub-Flavored Markdown extensions.
- **Image references**: use relative paths. Place images in `media/photos/`
  with sub-folders (`project/`, `reference/`).
- **Diagrams**: prefer Mermaid (in-markdown) for block diagrams; use annotated
  screenshots for schematics/layouts.

---

## Testing & Verification

For electronics changes, verify:

| Test | Equipment | What to Check |
|------|-----------|---------------|
| Power rails | Multimeter | 3.7 V input, 7.35 V boost, 5.0 V LDO |
| Oscillator output | Oscilloscope | ~4.8 Hz, 0 V to ~5 V square wave per NE555 |
| Phase inversion | 2-channel scope | NE555 OUT vs 74HC04 /OUT — complementary |
| Motor drive | Scope + motor | Forward/reverse voltage across motor terminals |

For mechanical changes, verify:

| Test | What to Check |
|------|---------------|
| Fit check | All parts assemble without interference |
| Motion test | Linkages rotate freely through full range of motion |
| Printability | No overhangs > 45°, no islands, no floating geometry |
| Weight | Assembly remains within target mass range |

---

## Reporting Bugs

Use the **Bug Report** issue template.

Include:
1. **What you expected** vs **what happened**
2. **Hardware details**: PCB revision, 3D-printer settings, supply voltage
3. **Measurements**: scope traces, multimeter readings
4. **Photos** of your board / assembly showing the issue
5. **Steps to reproduce**

---

## Feature Requests

Use the **Feature Request** issue template.

Consider these constraints when proposing:
- **No MCU / firmware** — this is a core design philosophy. Proposals requiring
  a microcontroller will likely be declined (but feel free to fork!).
- **Power budget** — total current draw is limited by the single-cell 3.7 V
  LiPo and MT3608 capability.
- **Weight budget** — every additional gram reduces available lift from the
  0.16 N reference figure.

---

## Questions

- **Build questions**: Open a GitHub Discussion (if enabled) or a regular Issue
  with the "question" label.
- **Design theory**: Refer to the reference paper (Qi & Zhang, 2020) included
  in `media/photos/reference/`.
- **Tool-specific questions**: Consult tool-specific forums (SolidWorks,
  EasyEDA, Altium) — we can't provide tool support, only design support.

---

**Thank you for contributing to Hsiwu!** 🦇
