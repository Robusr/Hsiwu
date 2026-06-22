# Changelog

All notable changes to the Hsiwu project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.0.0] — 2025-06-20

### Added

#### Electronics
- Complete system schematic: three NE555 astable multivibrator stages (~4.8 Hz
  square-wave output) → 74HC04 hex inverter for complementary phase generation
  → three L293D dual H-bridge motor drivers, each using one half-bridge channel
  per motor to produce continuous forward/reverse reciprocation.
- Power management block: MT3608 boost converter (3.7 V → 7.35 V for motors),
  on-board LDO (5 V for logic), Schottky diode reverse-polarity protection,
  slide power switch (SS12D07VG4) with green indicator LED.
- Custom irregularly-shaped PCB designed in EasyEDA (LCEDA project in
  `electronics/source/JLC/`) and re-captured in Altium Designer (`.schdoc` /
  `.pcbdoc` in `electronics/source/Altium Designer/`). PCB contour follows the
  internal mechanical frame curvature.
- Full Gerber manufacturing files (`electronics/output/gerber/`), pick-and-place
  data (`electronics/output/pick_and_place/`), and BOM export
  (`electronics/output/bom/`).

#### Mechanical
- Parametric 3D model of the full robot assembly in SolidWorks (`final_assembly.SLDASM`).
- Six linkage rod types (`rod_a`, `rod_bc`, `rod_d`, `rod_h`, `rod_ij`, `rod_kf`)
  forming the crank-rocker four-bar kinematic chains for each wing segment.
- Structural frame sub-assembly: central support, head support, tail bracket,
  lead screw, bearings, bearing caps, hinge pins.
- Motor & coupling sub-assembly: hollow-cup DC motor (4-stage planetary geared)
  and 2-to-3 shaft coupler.
- Fastener models: M2 countersunk screws (10/12/14/18 mm) and M2/M3 lock nuts.
- Full 3D PCB assembly with all electronic components placed for mechanical fit checking.
- Export files: STEP for 3D-printing (`mechanical/output/3dp/`), STL for laser
  cutting (`mechanical/output/laser/`).

#### Documentation
- `README.md` — full project overview in English, including system architecture,
  design specifications, replication guide, and visual assets.
- `README.zh-CN.md` — complete Chinese-language counterpart.
- Reference paper included (`media/photos/reference/仿生蝙蝠飞行器的设计制造_齐津浩.pdf`).

#### Media
- 3D renders: full assembly color render, front elevation, top view, isometric wireframe.
- PCB views: 3D render, 2D top/bottom layers, raw copper routing.
- Lead screw scissor mechanism animation (`screw_movement.gif`).
- Test & measurement photographs: assembly setup, soldering, oscilloscope test.

#### Repository Setup
- `.gitignore` — comprehensive rules for CAD/EDA/3D-printing project.
- `.gitattributes` — binary/text handling and diff configuration.
- `CONTRIBUTING.md` — contributor guidelines.
- `LICENSE.md` — MIT License.
- `.github/` — pull request template, issue templates, CODEOWNERS.

---

## Versioning Conventions

- **Major (X.0.0)**: Breaking changes to the physical design (mechanical
  dimensions, PCB form factor, connector pinout) that make it incompatible with
  previously manufactured parts.
- **Minor (0.X.0)**: New sub-assemblies, additional documentation, significant
  performance tuning, or new export formats.
- **Patch (0.0.X)**: Typo fixes, documentation corrections, minor BOM updates,
  image replacements.

---

[1.0.0]: https://github.com/Robusr/Hsiwu/releases/tag/v1.0.0
