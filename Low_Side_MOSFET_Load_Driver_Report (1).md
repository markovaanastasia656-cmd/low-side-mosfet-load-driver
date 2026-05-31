# Low-Side MOSFET Load Driver
## PCB Design and Layout — Final Examination Report

---

**University:** SRH University of Applied Sciences  
**Module:** PCB Design and Layout — Electronic Development Module  
**Project Title:** Low-Side MOSFET Load Driver  
**Instructor:** Prof. Dr. Oezdemir Cetin  
**KiCad Version:** KiCad 8.x  

---

## Declaration

I confirm that this project report and the submitted KiCad design files were prepared by me. All external sources, datasheets, figures, and references used in this project are cited. The submitted manufacturing files correspond to the PCB revision documented in this report.

---

## Abstract

This report documents the complete design of a logic-controlled low-side MOSFET load switch implemented as a two-layer FR4 printed circuit board. The board switches small DC loads — including resistive loads, LEDs, and small DC motors — at supply voltages up to 12 V. The load is enabled or disabled exclusively by a digital logic signal applied to connector J3, which is compatible with both 3.3 V and 5 V logic systems. The main switching device is an AO3400A N-channel MOSFET in a SOT-23 package, selected for its low gate-threshold voltage and an on-resistance of approximately 50 mΩ at logic-level drive.

Power trace widths of 2.0 mm were selected for a design current of 2.0 A, applying a 2× safety factor above the nominal 1.0 A load. Gate drive and protection networks include a 47 Ω series gate resistor and a 100 kΩ pull-down resistor. A Schottky flyback diode (SS14) protects against inductive load transients. An LED drain indicator and two test points were included to facilitate functional verification.

The PCB measures 39.37 mm × 27.94 mm and carries GND copper zones on both F.Cu and B.Cu layers. The design achieved zero ERC errors and zero DRC errors with zero DRC warnings at final submission. All manufacturing outputs including Gerber files, Excellon drill files, and a complete BOM were generated and verified.

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Requirements and Specifications](#2-requirements-and-specifications)
3. [Methods and Materials](#3-methods-and-materials)
4. [Schematic Design](#4-schematic-design)
5. [PCB Layout Design](#5-pcb-layout-design)
6. [EMC, Grounding, and Thermal Considerations](#6-emc-grounding-and-thermal-considerations)
7. [Manufacturing Output and DFM](#7-manufacturing-output-and-dfm)
8. [Bill of Materials and Sourcing](#8-bill-of-materials-and-sourcing)
9. [Results and Verification](#9-results-and-verification)
10. [Problems Encountered and Solutions](#10-problems-encountered-and-solutions)
11. [Discussion](#11-discussion)
12. [Conclusion](#12-conclusion)
13. [References](#13-references)

---

## List of Figures

| Figure | Caption | Source Screenshot |
|--------|---------|-------------------|
| Figure 1 | Final schematic of the Low-Side MOSFET Load Driver | `スクリーンショット_2026-05-31_061710.png` |
| Figure 2 | ERC result: 0 errors, 2 acknowledged net-label warnings | `スクリーンショット_2026-05-31_061749.png` |
| Figure 3 | Pre-defined track widths (0.25 mm, 2 mm) and via sizes configured in KiCad | `スクリーンショット_2026-05-31_061839.png` |
| Figure 4 | KiCad Board Setup — Design Rules Constraints panel | `スクリーンショット_2026-05-30_124649.png` |
| Figure 5 | Intermediate DRC result showing GND missing-connection errors on F.Cu | `スクリーンショット_2026-05-31_015000.png` |
| Figure 6 | Intermediate PCB layout state that produced the DRC errors in Figure 5 | `スクリーンショット_2026-05-31_015135.png` |
| Figure 7 | Final PCB layout with board outline and complete routing | `スクリーンショット_2026-05-31_012622.png` |
| Figure 8 | 3D Viewer — top view of the fully populated PCB | `スクリーンショット_2026-05-31_062012.png` |
| Figure 9 | 3D Viewer — bottom view showing through-hole connector and test-point pins | `スクリーンショット_2026-05-31_062022.png` |

---

## List of Tables

| Table | Caption |
|-------|---------|
| Table 1 | Project specification summary |
| Table 2 | Component list with exact footprints as used in KiCad |
| Table 3 | Design rules applied in KiCad |
| Table 4 | Pre-defined track widths and via sizes |
| Table 5 | Power dissipation calculations |
| Table 6 | Trace width design summary |
| Table 7 | Bill of Materials |
| Table 8 | Final verification results |

---

## 1. Introduction

A low-side MOSFET load switch is one of the fundamental building blocks in embedded electronics. The circuit places an N-channel MOSFET between the load's negative terminal and the system ground rail, allowing a logic-level signal to enable or disable power to a load without interrupting the high-side supply rail. This topology is well suited to applications where the logic controller shares a common ground with the load, and where the supply voltage exceeds the logic voltage — a condition that would complicate a direct high-side switch using an N-channel device.

This project implements a low-side switch for the following target applications:

- Resistive loads (heating elements, indicator lamps)
- LEDs driven at moderate current
- Small DC motors up to approximately 1 A continuous

The board accepts a 12 V worst-case supply and a logic input of 3.3 V or 5 V applied through connector J3. Load switching is performed entirely by the digital logic signal on J3; no mechanical switching element is present on the board. The circuit is designed to switch a nominal load current of 0.5–1.0 A with a design margin of 2×, yielding a design current of 2.0 A. Supporting circuitry includes a gate series resistor, a gate pull-down resistor, a flyback diode for inductive loads, an LED drain indicator, supply decoupling capacitors, and two test points for in-circuit measurement.

The design was completed in KiCad on a 2-layer FR4 PCB (1.6 mm thick, 1 oz copper) measuring 39.37 mm × 27.94 mm. The project was submitted as a complete manufacturing package including Gerber files, Excellon drill files, BOM, schematic, and this technical report.

This report is structured as follows: Section 2 defines the technical requirements; Section 3 describes the design methodology and tools; Sections 4 and 5 explain the schematic and PCB layout respectively; Sections 6 and 7 discuss EMC, grounding, thermal, and manufacturing considerations; Section 8 presents the BOM; Section 9 summarises the verification results; Section 10 details problems encountered and their solutions, including a dedicated subsection on the design iteration and debugging process; and Sections 11 and 12 provide discussion and conclusions.

---

## 2. Requirements and Specifications

The requirements were fixed before any component was selected or schematic drawn. Table 1 summarises the complete specification.

**Table 1: Project specification summary**

| Parameter | Value | Comment |
|-----------|-------|---------|
| Supply voltage (VIN) | 12 V DC | Worst-case operating voltage |
| Logic input voltage | 3.3 V or 5 V | Applied via J3; no mechanical switch on board |
| Nominal load current | 0.5–1.0 A | Continuous DC load |
| Design current (with safety factor) | 2.0 A | 2× safety margin above nominal |
| MOSFET V_DS minimum | 14.4 V | 1.2× worst-case V_IN; 30 V device selected |
| PCB technology | 2-layer FR4 | Standard prototype stackup |
| Board thickness | 1.6 mm | Standard PCB thickness |
| Copper weight | 1 oz (35 µm) | Standard copper weight |
| Board dimensions | 39.37 mm × 27.94 mm | Fixed board size |
| Power trace width | 2.0 mm | Sized for 2 A on 1 oz copper, ΔT ≤ 20 °C |
| Signal trace width | 0.25 mm | Gate, SIG, LED indicator nets |
| Ground plane | F.Cu + B.Cu copper zones | Continuous GND zones on both layers |
| Test points | 2 (TP1, TP2) | TP1 = Drain, TP2 = GND |
| ERC status at submission | 0 errors | See Figure 2 |
| DRC status at submission | 0 errors, 0 warnings | Verified at final submission |
| Load control method | Logic signal on J3 | No mechanical switching element |

---

## 3. Methods and Materials

### 3.1 Design Tool

The entire design was completed using KiCad (version 8.x). The schematic was drawn in the KiCad Schematic Editor; the PCB layout was developed in the KiCad PCB Editor. The 3D Viewer was used for mechanical and visual inspection (Figures 8 and 9). Electrical and design rule checks were performed using the integrated ERC and DRC engines.

### 3.2 PCB Technology Assumptions

The board was designed to the following stackup and fabrication parameters:

- Substrate: FR4
- Layers: 2 (F.Cu — component/top side; B.Cu — bottom side)
- Board thickness: 1.6 mm
- Copper weight: 1 oz (35 µm)
- Solder mask: Both sides
- Silkscreen: F.Cu (top side)

### 3.3 Design Rules

The KiCad Board Setup (Design Rules → Constraints) was configured as documented in Table 3 and visible in Figure 4.

**Table 3: Design rules applied in KiCad**

| Rule | Value | Rationale |
|------|-------|-----------|
| Minimum clearance | 0.2 mm | Compatible with standard PCB fabricators |
| Minimum track width | 0.2 mm | Fabricator minimum; actual signal traces use 0.25 mm |
| Minimum annular width | 0.1 mm | Standard for 1.6 mm FR4 |
| Minimum via diameter | 0.5 mm | Standard via size |
| Copper to hole clearance | 0.25 mm | Prevents annular ring violation |
| Copper to edge clearance | 0.5 mm | Standard board edge keep-out |
| Minimum drill size | 0.3 mm | Standard fabricator drill capability |
| Hole to hole clearance | 0.25 mm | Prevents drill-to-drill breakout |

### 3.4 Pre-defined Track and Via Sizes

Two track widths and two via sizes were configured in KiCad's Pre-defined Sizes panel, as shown in Figure 3.

**Table 4: Pre-defined track widths and via sizes**

| Type | Width / Diameter | Hole | Application |
|------|-----------------|------|-------------|
| Track | 0.25 mm | — | Signal nets (GATE, SIG, LED indicator) |
| Track | 2.0 mm | — | Power nets (VIN, DRAIN) |
| Via | 0.6 mm | 0.3 mm | Signal and general vias |
| Via | 0.8 mm | 0.4 mm | Power net vias |

### 3.5 Trace Width Rationale

Power trace width was determined by referencing IPC-2152 external conductor guidance for 1 oz copper, a design current of 2.0 A, and a target temperature rise of 10–20 °C. For 1 oz copper at 2 A, the IPC-2152 guidance indicates approximately 1.5–2.0 mm for external conductors at ΔT = 20 °C. A width of **2.0 mm** was selected for VIN and DRAIN traces to provide thermal margin and to accommodate current concentration at routing bends. Signal traces were set to **0.25 mm**, consistent with low-current gate, logic, and indicator nets carrying less than 5 mA.

### 3.6 Key Datasheets Referenced

- Alpha & Omega Semiconductor, *AO3400A — N-Channel Enhancement Mode Field Effect Transistor*, latest available revision.
- Vishay Semiconductors, *SS14 — Surface Mount Schottky Barrier Rectifier*, latest available revision.

---

## 4. Schematic Design

### 4.1 Overview

Figure 1 shows the final schematic. The design is organised into three functional blocks: the power input and decoupling block (left), the MOSFET switching block (centre), and the gate drive, indicator, and test point block (right). The schematic was drawn in KiCad Schematic Editor with net labels used to connect functional sections without crossing wires.

> **Figure 1** — Final schematic of the Low-Side MOSFET Load Driver  
> *(Source: `スクリーンショット_2026-05-31_061710.png`)*

The visible net labels in the schematic are: `VIN`, `GND`, `SIG`, `GATE`, `DRAIN`, `LOAD+`, `LOAD−`, and `SOURCE`. Two `PWR_FLAG` symbols are present — one on the VIN net at J1 and one on the GND net at J3 — as required by KiCad to satisfy the ERC power-pin connectivity check.

### 4.2 Power Input Block

Connector J1 (`PinHeader_1x02_P2.54mm_Vertical`) provides the 12 V supply. Pin 1 is connected to the VIN net; Pin 2 is connected to GND. Two decoupling capacitors — C1 (100 nF, `C_0603_1608Metric`) and C2 (10 µF, `C_0805_2012Metric`) — are connected in parallel between VIN and GND. C1 provides high-frequency decoupling for fast transient currents; C2 provides bulk energy storage for load-switching transients.

### 4.3 Load Connector and Flyback Diode

Connector J2 (`PinHeader_1x02_P2.54mm_Vertical`) is the load output terminal. Pin 1 is connected to the VIN net (labelled LOAD+); Pin 2 is connected to the DRAIN net (labelled LOAD−). The load is therefore connected in series with the drain of Q1 between VIN and GND, implementing the low-side switching topology.

Diode D1 (SS14, `Diode_SMD:D_SMA`) is connected across the load connector as a flyback clamp. The cathode of D1 connects to LOAD+ (VIN); the anode connects to LOAD− (DRAIN). When Q1 turns off, inductive energy stored in any reactive load is dissipated through D1 rather than appearing as a voltage spike on the DRAIN node. The SS14 was selected for its Schottky construction (fast recovery, low forward voltage ≈ 0.4 V), a 1 A forward current rating, and a 40 V reverse voltage — providing margin above the 12 V worst-case supply.

### 4.4 MOSFET Switching Stage

Q1 (AO3400A, `Package_TO_SOT_SMD:SOT-23`) is the primary switching device. The drain is connected to the DRAIN net; the source is connected to GND. The AO3400A was selected because:

- Gate threshold voltage V_GS(th) = 0.45–1.35 V (at 250 µA), confirming full enhancement at 3.3 V logic drive.
- On-resistance R_DS(on) ≈ 50 mΩ at V_GS = 4.5 V, giving a worst-case conduction loss of 0.20 W at 2 A design current — well within the SOT-23 thermal capability at the expected operating duty.
- Continuous drain current rating of 5.7 A at 25 °C (package limited), providing a 5× margin over the 1.0 A nominal load.
- V_DS rating of 30 V, satisfying the minimum requirement of 14.4 V (1.2× V_IN = 1.2 × 12 V) with significant margin.

The schematic shows the SOURCE net label on Q1's source pin. As documented in Section 9.1, this creates an ERC warning because the GND power symbol and the SOURCE label are simultaneously on the same node. This is discussed in full in that section.

### 4.5 Gate Drive Network

The gate drive network consists of two resistors:

**R1 (47 Ω, `R_0603_1608Metric`)** is connected in series between the SIG net (from J3 Pin 1) and the GATE net (Q1 gate). The 47 Ω value limits the gate charge current and the rate of V_GS rise, reducing dV/dt at the drain and limiting the high-frequency spectral content of the switching edge. For a DC on/off application, this value is appropriate and conservative.

**R2 (100 kΩ, `R_0603_1608Metric`)** is connected from the GATE net to GND. This pull-down resistor ensures Q1 remains firmly off if the logic input at J3 is disconnected, floated, or if the driving controller is in a high-impedance reset state. Without R2, a floating gate could allow partial enhancement of Q1 due to stray coupling, potentially causing uncontrolled load activation.

### 4.6 Logic Input Connector

J3 (`PinHeader_1x02_P2.54mm_Vertical`) provides the sole logic interface to the board. Pin 1 carries the SIG signal which drives the gate network; Pin 2 is GND, providing a local signal ground reference at the logic connector. The circuit is switched exclusively by the digital signal applied to J3 Pin 1. There is no mechanical switching element on the board.

### 4.7 LED Drain Indicator

The indicator circuit consists of R3 (3.3 kΩ, `R_0603_1608Metric`) and D2 (LED, `LED_SMD:LED_0805_2012Metric`), connected in series from the DRAIN net to GND. When Q1 is off, the DRAIN node is pulled to VIN through the load, forward-biasing D2 through R3. When Q1 is on, DRAIN is pulled near GND and D2 extinguishes. D2 therefore illuminates when the load is off and turns off when the load is on — a complementary state indicator.

LED current at 12 V supply:

```
I_LED = (V_DRAIN - V_f) / R3
      = (12 V − 2.0 V) / 3 300 Ω
      ≈ 3.0 mA
```

Power in R3:

```
P_R3 = I² × R = (0.003 A)² × 3 300 Ω = 0.030 W   [30 mW — within 100 mW 0603 rating]
```

### 4.8 Test Points

Two test points using footprint `TestPoint_THTPad_D1.0mm` are included:

- **TP1** — connected to the DRAIN net. This is the primary switching node and the most diagnostic point for functional verification with an oscilloscope.
- **TP2** — connected to GND. Providing a GND reference adjacent to TP1 allows reliable oscilloscope probing without hunting for a remote ground connection.

---

## 5. PCB Layout Design

### 5.1 Board Outline and Mechanical Constraints

The board outline is a closed rectangle on the `Edge.Cuts` layer measuring **39.37 mm × 27.94 mm**, visible in Figures 7, 8, and 9. The outline was verified to be fully closed by the DRC board-outline check at final submission. A copper-to-edge clearance of 0.5 mm is maintained throughout, consistent with the design rules in Figure 4.

### 5.2 Component Placement Strategy

Components were placed according to an intended left-to-right power-flow strategy, following the signal and current path from power input through decoupling and load switching to the drain output and gate drive circuits. This is visible in the final PCB layouts (Figures 7 and 8) and the 3D Viewer (Figures 8 and 9).

As visible in Figure 8 (3D top view), the placement groups are:

- **Left side:** J1 (power input connector), C1 and C2 (decoupling capacitors placed adjacent to J1)
- **Upper centre:** D1 (SS14 flyback diode, SMA package — visible as the large SMD body)
- **Upper right:** J2 (load output connector)
- **Centre:** Q1 (AO3400A SOT-23), R1 (47 Ω gate series), R2 (100 kΩ gate pull-down)
- **Lower centre-left:** J3 (logic input connector)
- **Right side:** D2 (LED indicator, 0805), R3 (3.3 kΩ, 0603), TP1 (Drain test point)
- **Lower centre:** TP2 (GND test point)

The silkscreen labels visible in Figure 8 confirm the net names at key connector pins: VIN, GND, LOAD+, LOAD−, SIG, GATE, DRAIN, confirming correct orientation and connectivity.

### 5.3 Routing Strategy

**High-current traces (VIN, DRAIN):** Power traces were routed at **2.0 mm** width. As visible in Figure 6 (intermediate layout) and Figure 7 (final layout), the red F.Cu traces for VIN and DRAIN are clearly wider than the signal traces running to the gate network. The VIN trace routes across the upper portion of the board from J1 to J2. The DRAIN trace connects J2 Pin 2 to the drain pad of Q1.

**Signal traces (GATE, SIG, LED path):** Signal traces were routed at **0.25 mm** width. The SIG trace from J3 to R1, the GATE trace from R1 to the Q1 gate, the R2 pull-down connection, and the LED indicator path all use 0.25 mm width. These nets carry less than 5 mA.

**Vias:** Via sizes used are 0.6 mm diameter / 0.3 mm hole and 0.8 mm diameter / 0.4 mm hole, as configured in the pre-defined sizes (Figure 3), consistent with the minimum drill rule of 0.3 mm (Figure 4).

### 5.4 Ground Plane Implementation

GND copper zones were applied to both F.Cu and B.Cu layers, both assigned to the GND net. The F.Cu GND zone fills the component-side copper between and around the SMD pads. The B.Cu GND zone provides the primary return plane on the bottom layer. The importance of including the F.Cu zone specifically — and the consequences of omitting it — are documented in detail in Section 10 (Problem 1).

The final layout with zone fill is visible in Figure 7, where the dark blue background represents the board outline and the B.Cu copper zone fill. The intermediate state before the F.Cu zone was added is visible in Figure 6, where the red (F.Cu only) copper fill is shown and the GND connections on the top layer were incomplete.

### 5.5 Silkscreen

Reference designators and net labels are included on the F.Cu silkscreen. As confirmed by the 3D Viewer (Figure 8), all labels are readable and positioned clear of pad areas and courtyard boundaries. Reference designator text was moved away from pads during the design iteration to resolve silkscreen clearance warnings, as documented in Section 10 (Problem 3).

### 5.6 3D Viewer Inspection

Figure 8 shows the top view from the KiCad 3D Viewer. All SMD components are visible on the top surface. The SOT-23 package (Q1), the SMA diode (D1), the 0805 capacitors and LED, the 0603 resistors, and the pin headers are all present and oriented consistently with the schematic. The test points TP1 and TP2 are visible as yellow pads. The board outline and GND copper fill are confirmed by the board shape.

Figure 9 shows the bottom view, confirming that the through-hole connector legs (J1, J2, J3) and test point pins protrude through the board and are available for soldering.

---

## 6. EMC, Grounding, and Thermal Considerations

### 6.1 Decoupling Capacitor Placement

C1 (100 nF, `C_0603_1608Metric`) and C2 (10 µF, `C_0805_2012Metric`) are placed adjacent to J1, the power input connector, minimising the impedance between the supply entry point and the decoupling capacitors. When Q1 switches and load current changes rapidly, the decoupling capacitors supply the transient current locally without requiring it to travel through the full supply wiring inductance from the external source.

The 100 nF ceramic (C1) provides low impedance at high frequencies (effective above approximately 1 MHz for X7R dielectric). The 10 µF bulk capacitor (C2) supplies energy for lower-frequency transients and ensures VIN remains stable during load switching.

### 6.2 Return Current Path and Loop Area Minimisation

The primary high-current conduction loop follows this path:

```
VIN (J1 Pin 1) → VIN trace → J2 Pin 1 (LOAD+)
                             → External load
                             → J2 Pin 2 (LOAD−/DRAIN) → DRAIN trace → Q1 Drain
                                                                      → Q1 Source → GND zone
                                                                                  → J1 Pin 2 → Supply return
```

The GND zone on B.Cu carries the return current directly beneath the forward (VIN) path on F.Cu. By keeping the forward and return conductors in proximity on adjacent copper layers, the enclosed area of the high-current loop is minimised. A smaller loop area reduces the magnetic flux associated with load-current switching, which lowers both common-mode and differential-mode radiated emissions.

### 6.3 Ground Plane Continuity

The GND zones on F.Cu and B.Cu are kept continuous across the board area. No signal traces were routed in a manner that interrupts the GND zone under the main power path. The gate signal trace and the J3 logic connector are located in the lower half of the board, physically separated from the VIN/DRAIN power path in the upper half, avoiding unintentional electromagnetic coupling between the fast-switching drain node and the gate drive trace.

### 6.4 Gate Drive and EMI

The 47 Ω series gate resistor (R1) limits the gate charge current and slows the rate at which V_GS rises. For a DC on/off application (not continuous PWM), 47 Ω is conservative and provides effective reduction of high-frequency spectral content at the drain switching edge without meaningfully affecting switching response time. R2 (100 kΩ) ensures Q1 remains off under any undefined or floating logic-input condition, eliminating the risk of uncontrolled partial enhancement.

The gate drive trace length is short — R1 is placed directly adjacent to the Q1 gate pin, as visible in Figure 8 — minimising gate trace inductance. This reduces the risk of oscillatory gate ringing at switch-on, which would cause higher peak drain currents and radiated emissions.

### 6.5 Thermal Analysis

Power dissipation in Q1 is dominated by conduction loss in R_DS(on). Table 5 summarises the computed dissipation.

**Table 5: Power dissipation calculations**

| Load Current | R_DS(on) | P = I² × R_DS(on) | Comment |
|-------------|----------|-------------------|---------|
| 1.0 A (nominal) | 50 mΩ | **0.05 W** | Nominal operating condition |
| 2.0 A (design maximum) | 50 mΩ | **0.20 W** | Design-current worst case |

At 0.20 W dissipated in a SOT-23 package (typical θ_JA ≈ 300 °C/W in still air), the theoretical junction temperature rise above ambient is:

```
ΔT_J = P × θ_JA = 0.20 W × 300 °C/W = 60 °C
```

At a 25 °C ambient this yields approximately 85 °C junction temperature, within the AO3400A rated maximum of 150 °C. In practice, the GND copper zones connected to the Q1 source pad provide additional thermal spreading beyond the package datasheet still-air assumption, reducing the effective thermal resistance and improving the margin further.

**Table 6: Trace width design summary**

| Net | Design Current | Selected Width | Rationale |
|-----|---------------|----------------|-----------|
| VIN | 2.0 A | 2.0 mm | IPC-2152 for 1 oz, ΔT ≤ 20 °C |
| DRAIN | 2.0 A | 2.0 mm | Same current path as VIN |
| GATE | < 5 mA | 0.25 mm | Signal current only |
| SIG | < 5 mA | 0.25 mm | Logic input |
| LED path | ~3 mA | 0.25 mm | Indicator only |

---

## 7. Manufacturing Output and DFM

### 7.1 Board Outline

The board outline is a closed rectangle on the `Edge.Cuts` layer measuring 39.37 mm × 27.94 mm, confirmed by the DRC board-outline check at final submission (see Section 9). The copper-to-edge clearance of 0.5 mm is maintained throughout.

### 7.2 Design Rules and Clearances

The design rules visible in Figure 4 are documented in Table 3. The actual minimum track width used (0.25 mm for signals, 2.0 mm for power) exceeds the 0.2 mm rule minimum, providing fabrication margin. The minimum drill size used is 0.3 mm for signal vias, exactly at the fabricator minimum, which is consistent with standard two-layer PCB services.

Drill sizes used in the design:

- Signal vias: 0.3 mm drill, 0.6 mm pad diameter
- Power vias: 0.4 mm drill, 0.8 mm pad diameter
- Test points (TP1, TP2): 1.0 mm through-hole pad (`TestPoint_THTPad_D1.0mm`)
- Through-hole connector pads (J1, J2, J3): standard 2.54 mm pitch per `PinHeader_1x02_P2.54mm_Vertical` footprint

### 7.3 Solder Mask and Silkscreen

Solder mask is applied to both layers. Silkscreen text was reviewed using the DRC and 3D Viewer to confirm no text overlaps with pads or component bodies. Reference designator positions were adjusted during the design iteration, as documented in Section 10 (Problem 3).

### 7.4 Gerber File Generation

Gerber files were generated from the KiCad PCB Editor using the standard KiCad Gerber output settings. The following layers were included:

- F.Cu (front copper)
- B.Cu (back copper)
- F.SilkS (front silkscreen)
- F.Mask (front solder mask)
- B.Mask (back solder mask)
- Edge.Cuts (board outline)

Excellon drill files were generated alongside the Gerber files.

### 7.5 Gerber Viewer Inspection

The generated Gerber files were inspected in a Gerber viewer prior to submission. The following checks were confirmed:

- Board outline is complete and closed on Edge.Cuts
- Copper pads are present on F.Cu and B.Cu at expected locations
- Solder mask openings correspond to pad locations
- Silkscreen text does not encroach on pads
- Drill hits correspond to pad centres

### 7.6 Assembly Considerations

All SMD components are on F.Cu only, enabling single-sided SMD assembly. Through-hole connectors (J1, J2, J3) and test points (TP1, TP2) are hand-solderable from the bottom side, as confirmed by the 3D bottom view in Figure 9. D2 (LED, 0805) has polarity indicated by the silkscreen. J1 Pin 1 (VIN) and J2 Pin 1 (LOAD+) are labelled on the silkscreen, as visible in Figure 8.

---

## 8. Bill of Materials and Sourcing

Table 7 provides the complete Bill of Materials using the exact footprints as placed in the KiCad PCB file.

**Table 7: Bill of Materials**

| Ref | Description | Value | Footprint | MPN (Example) | Qty | Key Rating |
|-----|-------------|-------|-----------|---------------|-----|------------|
| Q1 | N-Ch MOSFET | AO3400A | `Package_TO_SOT_SMD:SOT-23` | AO3400A | 1 | V_DS = 30 V; I_D = 5.7 A; R_DS(on) ≈ 50 mΩ at V_GS = 4.5 V |
| R1 | Gate series resistor | 47 Ω | `R_0603_1608Metric` | Any 47 Ω 1% 0603 | 1 | Limits gate charge current; 1/10 W rated |
| R2 | Gate pull-down | 100 kΩ | `R_0603_1608Metric` | Any 100 kΩ 1% 0603 | 1 | Holds gate low when J3 is disconnected |
| R3 | LED current limiter | 3.3 kΩ | `R_0603_1608Metric` | Any 3.3 kΩ 1% 0603 | 1 | I_LED ≈ 3 mA; P_R3 = 30 mW < 100 mW rating |
| C1 | Decoupling capacitor | 100 nF | `C_0603_1608Metric` | Any 100 nF 16 V X7R 0603 | 1 | 16 V rating > 12 V; X7R for stability |
| C2 | Bulk decoupling | 10 µF | `C_0805_2012Metric` | Any 10 µF 16 V X5R 0805 | 1 | 16 V rating > 12 V; bulk energy storage |
| D1 | Flyback diode | SS14 | `Diode_SMD:D_SMA` | SS14 (Vishay or equiv.) | 1 | Schottky; 1 A fwd; 40 V reverse |
| D2 | LED indicator | LED | `LED_SMD:LED_0805_2012Metric` | Any standard LED 0805 | 1 | Indicator only; ≈ 3 mA operating |
| J1 | Power input | 1×02 | `PinHeader_1x02_P2.54mm_Vertical` | Generic 2.54 mm header | 1 | 2 A current rating; standard pitch |
| J2 | Load output | 1×02 | `PinHeader_1x02_P2.54mm_Vertical` | Generic 2.54 mm header | 1 | 2 A current rating; standard pitch |
| J3 | Logic input | 1×02 | `PinHeader_1x02_P2.54mm_Vertical` | Generic 2.54 mm header | 1 | Logic signal only; < 5 mA |
| TP1 | Test point — Drain | — | `TestPoint_THTPad_D1.0mm` | Generic TH test point | 1 | 1.0 mm pad for oscilloscope probe |
| TP2 | Test point — GND | — | `TestPoint_THTPad_D1.0mm` | Generic TH test point | 1 | GND reference for probe |

**Total components:** 13 reference designators across 10 unique part types.

---

## 9. Results and Verification

Table 8 summarises the final verification status of the project.

**Table 8: Final verification results**

| Check | Result | Evidence |
|-------|--------|----------|
| Schematic completeness | Pass | Figure 1 — all nets connected, all reference designators assigned |
| ERC errors | **0 errors** | Figure 2 — green "0" error count confirmed |
| ERC warnings | 2 (acknowledged) | Figure 2 — two net-label warnings; see Section 9.1 |
| DRC errors | **0 errors** | Verified at final submission |
| DRC warnings | **0 warnings** | Verified at final submission |
| Board outline | Pass | Closed rectangle on Edge.Cuts; confirmed by DRC |
| GND copper zones | Pass | F.Cu and B.Cu zones present (Figures 7, 8) |
| Power trace width | Pass | VIN and DRAIN traces: 2.0 mm (Figures 6, 7) |
| Signal trace width | Pass | GATE, SIG, LED: 0.25 mm |
| Test points | Pass | TP1 (Drain), TP2 (GND) — visible in Figure 8 |
| 3D Viewer check | Pass | Figures 8 and 9 — components oriented correctly |
| Gerber files | Generated | F.Cu, B.Cu, F.SilkS, F.Mask, B.Mask, Edge.Cuts |
| Excellon drill files | Generated | PTH drill file included |
| BOM | Complete | Table 7; MPNs included for all components |

### 9.1 ERC Status — Net Label Warnings

The ERC, shown in Figure 2, reports **0 errors** and **2 warnings**. Both warnings arise from the same cause: KiCad detects that two different labels are attached to the same net node and notifies the designer which name will take precedence in the netlist.

The two warnings are:

1. **`Both DRAIN and LOAD- are attached to the same items; DRAIN will be used in netlists`**  
   The node at Q1 drain / J2 Pin 2 carries both the `DRAIN` net label and the `LOAD−` net label. Both are intentionally present — `LOAD−` describes the connector function while `DRAIN` describes the MOSFET node — but they refer to the same copper node. KiCad reports this as a warning, not an error, since both labels are electrically correct. The ERC engine will use `DRAIN` as the canonical net name in any exported netlist.

2. **`Both GND and SOURCE are attached to the same items; GND will be used in netlists`**  
   The node at Q1 source carries both the GND power symbol and a `SOURCE` net label. This is again intentional: `SOURCE` was added for schematic readability. KiCad correctly identifies these as the same node and will use `GND` as the canonical name.

These warnings are acknowledged and accepted. No functional or manufacturing consequence results from either warning, as both cases describe correct electrical connections. The ERC reports **0 errors**.

---

## 10. Problems Encountered and Solutions

### 10.1 Design Iteration and Debugging Process

The layout was developed iteratively, with DRC run at each significant milestone. The debugging process followed these stages:

1. **Initial schematic capture and ERC** — The schematic was drawn and the ERC was run to confirm net connectivity. The two label-conflict warnings documented in Section 9.1 were identified and accepted at this stage.

2. **Component import and initial placement** — All 13 components were imported into the PCB editor and placed according to the intended power-flow strategy.

3. **Routing of power and signal traces** — VIN and DRAIN traces were routed at 2.0 mm; gate, signal, and indicator traces were routed at 0.25 mm.

4. **First DRC run — GND connectivity errors identified** — A GND copper zone was added to B.Cu only. Running DRC at this point revealed multiple missing-connection errors on F.Cu GND pads (Problem 1 below). These were resolved before proceeding.

5. **Board outline added — outline error resolved** — The Edge.Cuts outline had not yet been drawn, triggering a board-outline DRC error (Problem 2 below). The rectangle was drawn and the error cleared.

6. **Silkscreen clearance review** — Running DRC with silkscreen checks enabled revealed overlapping text (Problem 3 below). Reference designators were repositioned.

7. **Final DRC and ERC** — All checks confirmed: 0 DRC errors, 0 DRC warnings, 0 ERC errors (2 acknowledged warnings).

8. **3D Viewer inspection** — Final visual check of component orientations, silkscreen readability, and board shape.

---

### Problem 1: Missing GND Connections on F.Cu (DRC Errors)

**Symptom:** The DRC (Figure 5) reported six "Missing connection between items" errors, all involving GND-net pads on F.Cu. The specific errors visible in Figure 5 include:

- Pad 2 [GND] of C1 on F.Cu ↔ PTH pad 2 [GND] of J1
- Pad 2 [GND] of C2 on F.Cu ↔ Pad 2 [GND] of C1 on F.Cu
- Pad 1 [GND] of TP2 on F.Cu ↔ Pad 2 [GND] of R2 on F.Cu
- Pad 2 [GND] of R2 on F.Cu ↔ Pad 2 [GND] of Q1 on F.Cu

Figure 6 shows the PCB layout at the state that produced these errors: the red F.Cu copper fill is present, but there is no continuous GND copper zone connecting the SMD GND pads on the component side.

**Root cause:** Only a B.Cu GND copper zone had been created at this stage. All SMD components (C1, C2, R1, R2, Q1, D2, TP2) have their GND pads on F.Cu. A zone existing exclusively on B.Cu does not provide any copper connection between SMD pads on the opposite layer. Despite all pads sharing the GND net in the netlist, no physical copper connected them on the component side, causing KiCad's DRC to correctly flag them as missing connections.

This is a critical issue: a board manufactured in this state would have floating GND connections for C1, C2, R2, Q1 source, and TP2, making the circuit completely non-functional.

**Solution:** A second GND copper zone was added on F.Cu, assigned to the GND net. After running "Fill All Zones," the F.Cu zone connected all SMD GND pads on the component side. All six missing-connection DRC errors were resolved. The final state is visible in Figure 7, where the B.Cu background (dark blue) and the F.Cu zone fill together provide complete GND connectivity on both layers.

**Key lesson:** In a 2-layer design with SMD components on F.Cu, a GND zone must be placed on F.Cu as well as B.Cu. A single bottom-layer ground plane does not connect top-layer SMD GND pads unless explicit traces or vias are also routed. Relying on a single B.Cu zone and running DRC before placing the F.Cu zone produces misleading airwire clearance appearance in the routed view but fails the connectivity check when zone-refill DRC is run.

---

### Problem 2: Missing Board Outline on Edge.Cuts

**Symptom:** The DRC reported "Board has malformed outline (no edges found on Edge.Cuts layer)."

**Root cause:** The board outline had not yet been drawn when DRC was first run. The `Edge.Cuts` layer was empty.

**Solution:** A closed rectangular board outline measuring 39.37 mm × 27.94 mm was drawn on the `Edge.Cuts` layer using the KiCad PCB Editor rectangle tool. The DRC board-outline error was cleared on the next run. The completed board outline is visible in Figures 7, 8, and 9.

---

### Problem 3: Silkscreen Clearance Warnings

**Symptom:** The DRC reported silkscreen clearance violations where reference designator or value text overlapped component pads or courtyard boundaries.

**Root cause:** Default KiCad reference designator positions place text in a location that may overlap pads of adjacent or very small components, particularly for 0603-size resistors and the SOT-23 package. With 13 components on a compact 39.37 mm × 27.94 mm board, the default text positions produced several overlaps.

**Solution:** All reference designators and value labels were manually repositioned to clear pad areas and component courtyard boundaries. Text was oriented horizontally where space permitted. After repositioning, the DRC reported **0 warnings** related to silkscreen clearance. The final text positions are visible in the 3D Viewer top view (Figure 8), where all labels are readable without encroaching on component bodies.

---

## 11. Discussion

### 11.1 Successful Design Decisions

The placement strategy — positioning decoupling capacitors C1 and C2 immediately adjacent to the power input connector J1 — results in the shortest possible high-frequency current path between the supply entry and the decoupling capacitors. Any transient current spike caused by Q1 switching is absorbed locally by C1 and C2 rather than being drawn through the full supply cable inductance.

The inclusion of D1 (SS14 flyback diode) across the load connector is conservative but appropriate for a general-purpose switching board. Because J2 is exposed to the user and the board might be connected to relay coils, solenoids, or DC motors during service, protecting the DRAIN node from inductive transients at the circuit level eliminates dependence on the end user's awareness of flyback hazards.

The LED indicator connected to the DRAIN node rather than the gate provides a true indication of the switched load state rather than merely reflecting the logic input. If Q1 were to fail short, the gate indicator would show the input signal correctly while a drain indicator correctly shows the failed-on state.

### 11.2 Problems and Their Design Impact

The most significant problem encountered was Problem 1 (missing GND connections on F.Cu). Had this not been identified through DRC, the manufactured board would have had floating GND pads for C1, C2, Q1 source, and R2, making the circuit completely non-functional. The issue was not visible from the schematic — all net assignments were correct — and was only detectable by running DRC with zone refill enabled. This highlights the importance of enabling "Refill all zones before performing DRC" in the DRC settings (visible checked in Figure 5), rather than relying on visual inspection of the routed view.

Problem 2 (missing board outline) is a straightforward oversight that DRC catches unambiguously. The critical discipline it reinforces is to define the Edge.Cuts outline early in the layout process, before beginning component placement, to avoid component placement that later conflicts with a changed outline.

Problem 3 (silkscreen clearance warnings) had no functional consequence but is important for manufacturability: silkscreen ink printed over solder pads degrades pad solderability. KiCad's silkscreen DRC check provides a direct quality gate for this.

### 11.3 Compromises and Limitations

The SOT-23 package was selected for Q1 for board compactness. At the 2 A design maximum and 50 mΩ R_DS(on), the theoretical junction temperature rise is approximately 60 °C (still-air assumption). For sustained operation near 2 A in a high-ambient environment, a larger package such as SOT-223 or DPAK would reduce the junction temperature rise significantly and provide better thermal margin. For the nominal 1 A load, the SOT-23 is adequate.

The use of standard 2.54 mm pin headers for J1 and J2 limits the practical current rating of the power connectors. For sustained loads near the 2 A design limit, screw terminal connectors would provide greater mechanical robustness and a higher current rating per contact.

### 11.4 Potential Future Improvements

- **Screw terminal connectors for J1 and J2:** Improve mechanical robustness and current capacity for power connections.
- **Additional test point on GATE net:** A dedicated test point on the GATE node (currently only probe-accessible via R1 pads) would allow direct oscilloscope measurement of the gate voltage waveform.
- **Larger MOSFET package for sustained high-current use:** SOT-223 or DPAK for improved thermal margin at 2 A continuous.
- **TVS clamp on VIN:** For harsh supply environments, a TVS device across J1 would provide protection against supply voltage transients.

---

## 12. Conclusion

The Low-Side MOSFET Load Driver PCB was designed, iteratively debugged, verified, and prepared for manufacturing within the constraints of the project specification. The board measures 39.37 mm × 27.94 mm on a 2-layer FR4 substrate (1.6 mm, 1 oz copper) and implements a complete logic-controlled DC load switch using an AO3400A N-channel MOSFET. Load switching is performed exclusively by a digital logic signal applied to connector J3; no mechanical switching element is present on the board.

All design objectives were met:

- Low-side switch topology correctly implemented for 12 V loads up to 1 A nominal (2 A design margin).
- Power traces sized to 2.0 mm for the 2 A design current in accordance with IPC-2152 guidance.
- Gate series resistor (R1 = 47 Ω) and gate pull-down resistor (R2 = 100 kΩ) correctly implemented.
- SS14 flyback diode protects against inductive load transients.
- Dual GND copper zones on F.Cu and B.Cu provide complete GND connectivity on both layers.
- Two test points provided: TP1 on the DRAIN net, TP2 on GND.
- Final submission: **0 ERC errors**, **0 DRC errors**, **0 DRC warnings**.
- Complete manufacturing package: Gerber files, Excellon drill files, BOM, schematic, and 3D Viewer screenshots.

The most important technical lesson from this project is that a GND copper zone must be placed on every copper layer that contains SMD GND pads. A single bottom-layer GND plane leaves all top-side SMD GND pads electrically unconnected on the copper layer — a failure mode that is invisible to visual inspection but reliably detected by running DRC with zone refill enabled. This insight drove the addition of the F.Cu GND zone and resolved all six DRC connectivity errors that were identified at the intermediate design stage.

The design is manufacturable using standard two-layer PCB fabrication, and the documentation is sufficient for another engineer to review, build, and test the board without additional clarification.

---

## 13. References

[1] Alpha & Omega Semiconductor, *AO3400A — N-Channel Enhancement Mode Field Effect Transistor Datasheet*, latest available revision. [Online]. Available: https://www.aosmd.com/

[2] Vishay Semiconductors, *SS14 — Surface Mount Schottky Barrier Rectifier Datasheet*, latest available revision. [Online]. Available: https://www.vishay.com/

[3] IPC, *IPC-2152 — Standard for Determining Current Carrying Capacity in Printed Board Design*, IPC International.

[4] KiCad Documentation Team, *KiCad EDA Documentation*, KiCad version 8.x. [Online]. Available: https://docs.kicad.org/

[5] SRH University of Applied Sciences, *PCB Design and Layout — Final Exam Brief*, Electronic Development Module, Prof. Dr. Oezdemir Cetin.

[6] Component manufacturers, *Relevant component datasheets for 0603/0805 passive components (resistors, capacitors, LEDs)*, latest available revisions from distributor datasheets (Mouser, Digi-Key, LCSC).

---

## Appendix A — Figure Reference Index

The following table maps each figure in this report to the exact screenshot filename from the project archive.

| Figure | Screenshot Filename | Content |
|--------|--------------------|---------| 
| Figure 1 | `スクリーンショット_2026-05-31_061710.png` | Final schematic (KiCad Schematic Editor) |
| Figure 2 | `スクリーンショット_2026-05-31_061749.png` | ERC result: 0 errors, 2 warnings |
| Figure 3 | `スクリーンショット_2026-05-31_061839.png` | Pre-defined track widths and via sizes |
| Figure 4 | `スクリーンショット_2026-05-30_124649.png` | Board Constraints panel (Design Rules) |
| Figure 5 | `スクリーンショット_2026-05-31_015000.png` | DRC intermediate: GND missing-connection errors |
| Figure 6 | `スクリーンショット_2026-05-31_015135.png` | Intermediate PCB layout (B.Cu zone only, pre-outline) |
| Figure 7 | `スクリーンショット_2026-05-31_012622.png` | Final PCB layout with board outline |
| Figure 8 | `スクリーンショット_2026-05-31_062012.png` | 3D Viewer — top view |
| Figure 9 | `スクリーンショット_2026-05-31_062022.png` | 3D Viewer — bottom view |

---

*End of Report*
