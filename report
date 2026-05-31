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

This report documents the complete design of a logic-controlled low-side MOSFET load switch implemented as a two-layer FR4 printed circuit board. The board switches small DC loads — including resistive loads, LEDs, and small DC motors — at supply voltages up to 12 V, with a logic input compatible with both 3.3 V and 5 V systems. The main switching device is an AO3400A N-channel MOSFET in a SOT-23 package, selected for its low gate-threshold voltage and low on-resistance of approximately 50 mΩ at logic-level drive.

The design methodology followed IPC-2152 principles for power trace sizing, with a 2.0 mm trace width selected for a design current of 2.0 A (a 2× safety factor above the nominal 1.0 A load). Gate drive and protection networks were designed with a 47 Ω series gate resistor and a 100 kΩ pull-down resistor. A Schottky flyback diode (SS14) protects against inductive load transients. An LED indicator and two test points were included to facilitate functional verification.

The PCB measures 39.37 mm × 27.94 mm and carries dual GND copper zones on both F.Cu and B.Cu layers. The design achieved zero ERC errors and zero DRC errors and zero DRC warnings at final submission. All manufacturing outputs including Gerber files, Excellon drill files, and a complete BOM were generated and verified.

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

| Figure | Caption |
|--------|---------|
| Figure 1 | Final schematic of the Low-Side MOSFET Load Driver |
| Figure 2 | KiCad ERC result showing 0 errors (net-label warnings acknowledged) |
| Figure 3 | Top-layer PCB layout with F.Cu GND zone fill visible |
| Figure 4 | Final PCB layout with board outline and component placement |
| Figure 5 | KiCad DRC result at an intermediate stage showing missing GND connections |
| Figure 6 | KiCad Board Constraints (Design Rules) settings |
| Figure 7 | Pre-defined track widths: 0.25 mm signal and 2.0 mm power |
| Figure 8 | 3D Viewer — bottom view of the populated PCB |
| Figure 9 | 3D Viewer — top view of the populated PCB |

---

## List of Tables

| Table | Caption |
|-------|---------|
| Table 1 | Project specification summary |
| Table 2 | Component list with footprints |
| Table 3 | Design rules applied in KiCad |
| Table 4 | Power dissipation calculations |
| Table 5 | Trace width design |
| Table 6 | Bill of Materials |
| Table 7 | Final verification results |

---

## 1. Introduction

A low-side MOSFET load switch is one of the fundamental building blocks in embedded electronics. The circuit places an N-channel MOSFET between the load's negative terminal and the system ground rail, allowing a logic-level signal to enable or disable power to a load without interrupting the high-side supply rail. This topology is particularly well suited to applications where the logic controller shares a common ground with the load, and where the supply voltage exceeds the logic voltage — a condition that would make a simple transistor switch more complex on the high side.

This project implements such a circuit for use with the following target applications:

- Resistive loads (heating elements, indicator lamps)
- LEDs driven at moderate current
- Small DC motors up to approximately 1 A continuous

The board accepts a 12 V supply in the worst case, a logic input of 3.3 V or 5 V, and is designed to switch a nominal load current of 0.5–1.0 A with a design margin of 2× yielding a design current of 2.0 A. The circuit includes a gate series resistor, a gate pull-down resistor, a flyback diode for inductive loads, an LED drain indicator, supply decoupling capacitors, and two test points for in-circuit measurement.

The design was completed entirely in KiCad on a 2-layer FR4 PCB (1.6 mm thick, 1 oz copper) measuring 39.37 mm × 27.94 mm. The project was submitted as a complete manufacturing package including Gerber files, Excellon drill files, BOM, schematic PDF, and this technical report.

This report is structured as follows: Section 2 defines the technical requirements; Section 3 describes the design methodology and tools; Sections 4 and 5 explain the schematic and PCB layout respectively; Sections 6 and 7 discuss EMC, grounding, thermal, and manufacturing considerations; Section 8 presents the BOM; Section 9 summarises the verification results; Section 10 details problems encountered and their solutions; and Sections 11 and 12 provide discussion and conclusions.

---

## 2. Requirements and Specifications

The requirements were fixed before any component was selected or schematic drawn. Table 1 summarises the complete specification.

**Table 1: Project specification summary**

| Parameter | Value | Comment |
|-----------|-------|---------|
| Supply voltage (VIN) | 12 V DC | Worst-case operating voltage |
| Logic input voltage | 3.3 V or 5 V | Dual-compatible logic threshold |
| Nominal load current | 0.5–1.0 A | Continuous DC load |
| Design current (with safety factor) | 2.0 A | 2× safety margin applied |
| PCB technology | 2-layer FR4 | Standard prototype stackup |
| Board thickness | 1.6 mm | Standard PCB thickness |
| Copper weight | 1 oz (35 µm) | Standard copper weight |
| Board dimensions | 39.37 mm × 27.94 mm | Fixed by mechanical constraint |
| Minimum power trace width | 2.0 mm | Calculated for 2 A, 1 oz, ΔT ≤ 20 °C |
| Minimum signal trace width | 0.25 mm | Standard for low-current signal nets |
| Ground plane | Required on both layers | Continuous GND zones on F.Cu and B.Cu |
| Test points | ≥ 1 required | TP1 (Drain), TP2 (GND) provided |
| ERC status | 0 errors | Zero unresolved ERC errors at submission |
| DRC status | 0 errors, 0 warnings | Zero unresolved DRC errors at submission |
| MOSFET VDS rating | ≥ 14.4 V (1.2× VIN) | Margin above 12 V; 20 V minimum selected |
| Switching type | DC on/off | Not PWM; no switching loss analysis required |

---

## 3. Methods and Materials

### 3.1 Design Tool

The entire design was completed using KiCad (version 8.x), an open-source EDA suite. The schematic was drawn in the KiCad Schematic Editor and the PCB layout was developed in the KiCad PCB Editor. The 3D Viewer was used for mechanical inspection. Design rule checks were performed using the integrated ERC and DRC engines.

### 3.2 PCB Technology Assumptions

The board was designed to the following stackup and fabrication parameters:

- Substrate: FR4
- Layers: 2 (F.Cu, B.Cu)
- Board thickness: 1.6 mm
- Copper weight: 1 oz (35 µm)
- Solder mask: Both sides
- Silkscreen: F.Cu silkscreen only (primary component side)

### 3.3 Design Rules

The KiCad Board Setup (Design Rules → Constraints) was configured as shown in Table 3 and confirmed visible in Figure 6.

**Table 3: Design rules applied in KiCad**

| Rule | Value | Rationale |
|------|-------|-----------|
| Minimum clearance | 0.2 mm | Compatible with standard PCB fabs |
| Minimum track width | 0.2 mm | Fab minimum; signal traces use 0.25 mm |
| Minimum via diameter | 0.5 mm | Standard via for 1.6 mm FR4 |
| Minimum drill size | 0.3 mm | Standard fab drill capability |
| Hole-to-hole clearance | 0.25 mm | Prevents drill breakout |
| Copper to edge clearance | 0.5 mm | Standard edge keep-out |

Two pre-defined trace widths were configured in KiCad's Pre-defined Sizes panel:

- **0.25 mm** — for signal, gate, and LED indicator traces
- **2.0 mm** — for power (VIN, DRAIN) traces

This is visible in Figure 7.

### 3.4 Trace Width Calculation

Power trace width was determined using IPC-2152 external conductor guidance for 1 oz copper, a design current of 2.0 A, and a target temperature rise of 10–20 °C. For 1 oz copper at 2 A and a ΔT ≤ 20 °C, the required trace width falls in the range of approximately 1.5–2.0 mm for external conductors. A width of **2.0 mm** was selected to provide margin and to accommodate current concentrations at corners. Signal traces were set to **0.25 mm**, consistent with low-current gate and indicator nets.

### 3.5 Key Datasheets Referenced

- Alpha & Omega Semiconductor, *AO3400A — N-Channel Enhancement Mode Field Effect Transistor*, latest available revision.
- Vishay, *SS14 — Surface Mount Schottky Barrier Rectifier*, latest available revision.

---

## 4. Schematic Design

### 4.1 Overview

The schematic (Figure 1) is organised into three functional blocks: the power input and decoupling block, the MOSFET switching block, and the gate drive and indicator block.

> **Figure 1** — Final schematic of the Low-Side MOSFET Load Driver (see schematic screenshot, file `スクリーンショット_2026-05-31_012622.png`)

### 4.2 Power Input Block

Connector J1 (PinHeader 1×02, 2.54 mm pitch) provides the 12 V supply. Pin 1 is connected to the VIN net; Pin 2 is connected to GND. Two decoupling capacitors — C1 (100 nF, 0603) and C2 (10 µF, 0805) — are connected in parallel between VIN and GND immediately adjacent to J1. C1 provides high-frequency decoupling for fast transient currents; C2 provides bulk energy storage to absorb slower load-switching transients. A PWR_FLAG symbol is attached to the VIN net and to GND to satisfy KiCad's power-net connectivity rules.

### 4.3 Load Connector and Flyback Diode

Connector J2 (PinHeader 1×02, 2.54 mm pitch) serves as the load output terminal. Pin 1 is connected to VIN (LOAD+); Pin 2 is connected to the DRAIN net (LOAD−). The load is therefore connected in series with the drain of Q1 between VIN and GND, implementing a low-side switching topology.

Diode D1 (SS14, D_SMA) is connected across the load connector as a flyback diode. The cathode of D1 is connected to LOAD+ (VIN); the anode is connected to LOAD− (DRAIN). When Q1 turns off, any inductive energy stored in the load is dissipated through D1 rather than appearing as a voltage spike on the DRAIN node. The SS14 was selected for its Schottky construction (fast recovery, low forward voltage), 1 A forward current rating, and 40 V reverse voltage — providing adequate margin above the 12 V supply.

### 4.4 MOSFET Switching Stage

Q1 (AO3400A, SOT-23) is the primary switching device. The drain is connected to the DRAIN/LOAD− net; the source is connected to GND. The AO3400A was selected because:

- Its gate threshold voltage (V_GS(th)) is 0.45–1.35 V at 250 µA, confirming full enhancement at 3.3 V logic drive.
- Its on-resistance is specified at approximately 50 mΩ at V_GS = 4.5 V, yielding a worst-case power dissipation of 0.20 W at 2 A design current — well within the SOT-23 package capability.
- Its continuous drain current rating of 5.7 A at 25 °C (package limited) provides a 5× margin over the nominal 1.0 A load.
- Its V_DS rating of 30 V provides margin above the 12 V worst-case supply, satisfying the minimum V_DS requirement of 14.4 V (1.2× V_IN) computed in the requirements phase.

### 4.5 Gate Drive Network

The gate drive network consists of two resistors:

**R1 (47 Ω)** is connected in series between the SIG net (from J3 Pin 1) and the GATE net (Q1 gate). The 47 Ω value limits the gate charge current and the rate of V_GS rise, reducing the dV/dt at the drain and thereby reducing electromagnetic interference from fast switching edges. For a DC on/off application (not PWM), 47 Ω is appropriate.

**R2 (100 kΩ)** is connected from the GATE net to GND. This pull-down resistor ensures Q1 remains off if the logic input is disconnected or the driving controller is in a high-impedance reset state. Without this resistor, a floating gate could allow partial enhancement of Q1 due to noise, which might cause uncontrolled load activation.

### 4.6 Logic Input Connector

J3 (PinHeader 1×02, 2.54 mm pitch) provides the logic interface. Pin 1 carries the SIG (gate drive) signal; Pin 2 is GND, providing a local signal ground reference at the connector. A second PWR_FLAG is attached to the J3 GND pin for ERC compliance.

### 4.7 LED Drain Indicator

The LED indicator circuit consists of R3 (3.3 kΩ) and D2 (LED, 0805), connected in series from the DRAIN net to GND. When Q1 is off, the DRAIN node is pulled to VIN (12 V) through the load, forward-biasing D2 through R3. When Q1 is on, the DRAIN node is pulled near GND, and D2 turns off. Therefore D2 illuminates when the load is off and extinguishes when the load is on — providing a complementary load-state indicator.

The R3 value is calculated as:

```
I_LED = (V_DRAIN - V_f) / R3
      = (12 V - 2.0 V) / 3300 Ω
      ≈ 3.0 mA
```

This current is appropriate for a standard indicator LED (typical operating current 2–20 mA) and keeps power dissipation in R3 well within the 0603 package rating (100 mW maximum):

```
P_R3 = I² × R = (0.003)² × 3300 = 0.030 W   [30 mW — within rating]
```

### 4.8 Test Points

Two test points are included:

- **TP1** (TestPoint_THTPad_D1.0mm) — connected to the DRAIN net. This is the primary switching node and the most diagnostic point for functional verification.
- **TP2** (TestPoint_THTPad_D1.0mm) — connected to GND. Providing a local GND reference adjacent to TP1 avoids the need to probe remote ground pads with an oscilloscope.

### 4.9 Net Naming

The primary nets are: `VIN`, `GND`, `SIG`, `GATE`, `DRAIN`, `LOAD+`. The DRAIN and LOAD− labels are assigned to the same node (the Q1 drain / J2 Pin 2). During ERC checking, KiCad flagged this as a warning (two labels on one node). This is functionally correct and was acknowledged (see Section 10, Problem 2).

---

## 5. PCB Layout Design

### 5.1 Board Outline and Mechanical Constraints

The board outline is defined as a closed rectangle on the `Edge.Cuts` layer with dimensions **39.37 mm × 27.94 mm**. The outline was verified to be fully closed with no gaps, satisfying KiCad's DRC board-outline check.

No mounting holes are required by the specification. The copper-to-edge clearance is maintained at 0.5 mm throughout.

### 5.2 Component Placement Strategy

Placement followed a left-to-right power flow, minimising the length of the high-current path:

```
J1 (Power Input) → C1, C2 (Decoupling) → D1 (Flyback) → J2 (Load) → Q1 (MOSFET)
```

This arrangement is visible in Figure 3 and Figure 4. The key placement decisions are as follows:

- **J1** is placed at the upper left to receive the supply cable from the left side of the board.
- **C1 and C2** are placed immediately adjacent to J1 on the right, minimising the loop area between the supply connector and the decoupling capacitors.
- **D1** is placed near J2 so that the flyback current loop — from D1 anode through J2 Pin 2 and back via D1 cathode to J2 Pin 1 — is kept short and compact.
- **J2** is placed at the upper right as the load output connector.
- **Q1** is placed centrally, adjacent to J2, so that the drain trace from J2 to Q1 is short and the source connection to the GND plane is immediate.
- **R1 and R2** are placed directly beside the gate pin of Q1, minimising the GATE net stub length between the gate resistor and Q1. This reduces gate trace inductance and the associated potential for ringing.
- **J3** (logic header) is placed in the lower-left area, physically separated from the power path.
- **R3 and D2** (LED indicator) are placed to the right of Q1, close to the drain node.
- **TP1** is placed at the far right, connected to the DRAIN net, accessible for a probe without risk of slipping onto adjacent power pads.
- **TP2** is placed in the lower centre, providing a GND reference for probe measurement.

### 5.3 Routing Strategy

**High-current traces (VIN, DRAIN):** All power traces were routed at **2.0 mm** width. The VIN trace runs from J1 Pin 1 across the top of the board to J2 Pin 1. The DRAIN trace runs from J2 Pin 2 down to the drain pad of Q1. Both are short, direct, and avoid unnecessary bends.

**Signal traces (GATE, SIG, LED path):** All signal traces were routed at **0.25 mm** width. The SIG trace from J3 to R1, the GATE trace from R1 to Q1 gate, the R2 pull-down, and the LED indicator path (DRAIN → R3 → D2 → GND) all use 0.25 mm width. These carry less than 5 mA and require no additional width.

**Vias:** Vias were used where necessary to transition between layers. Via sizes are: diameter 0.6 mm, hole 0.3 mm (standard) and 0.8 mm diameter, 0.4 mm hole (larger vias for power nets), consistent with the pre-defined via sizes configured in KiCad (visible in Figure 7).

### 5.4 Ground Plane Implementation

A GND copper zone was applied to both F.Cu and B.Cu. The zones are both assigned to the GND net and connected together where necessary. The F.Cu GND zone fills the space between components on the top layer, connecting all GND pads on the component side. The B.Cu GND zone provides the primary return plane for the bottom layer.

This dual-plane approach was necessitated by a DRC problem encountered during layout (see Section 10, Problem 3): with only a single B.Cu GND zone, the SMD GND pads on F.Cu were not connected by copper, causing multiple "missing connection" DRC errors. Adding the F.Cu zone resolved all errors.

### 5.5 Silkscreen

All reference designators (J1, J2, J3, Q1, R1, R2, R3, C1, C2, D1, D2, TP1, TP2) are included on the F.Cu silkscreen layer. Connector polarity labels (VIN+, GND, LOAD+, LOAD−, SIG) are included for assembly and test clarity. Reference designators and value labels were moved away from pads to avoid silkscreen-on-pad DRC violations (see Section 10, Problem 5).

### 5.6 3D Viewer Inspection

The 3D Viewer was used to confirm component orientation, silkscreen readability, and overall mechanical fit. Figure 8 (bottom view) and Figure 9 (top view) show the final board state. All through-hole connector pads are visible from the bottom; SMD components are correctly oriented on the top surface. The board outline is clean and the copper fill is visible through the PCB substrate colour.

---

## 6. EMC, Grounding, and Thermal Considerations

### 6.1 Decoupling Capacitor Placement

C1 (100 nF ceramic) and C2 (10 µF electrolytic-type) were placed immediately adjacent to J1, the power input connector. This minimises the impedance between the supply entry point and the decoupling capacitors. When Q1 switches and the load current changes rapidly, the decoupling capacitors supply the transient current locally without drawing it through the long supply wiring inductance from the power source.

The 100 nF ceramic (C1) provides low impedance at high frequencies (typically effective above 1 MHz). The 10 µF bulk capacitor (C2) provides energy for lower-frequency transients and ensures VIN remains stable during load switching.

### 6.2 Return Current Path and Loop Area Minimisation

The primary high-current loop in this design is:

```
VIN (J1) → VIN trace → LOAD (J2) → External load → J2 Pin 2 → DRAIN trace → Q1 drain → Q1 source → GND plane → J1 GND → supply return
```

The GND return current from Q1 source flows through the GND copper zone back to J1. By placing the GND zone directly under the VIN trace on the opposite layer (B.Cu), the forward current and return current paths are closely parallel, minimising the enclosed loop area. A smaller loop area reduces radiated magnetic field emissions, which is particularly important during the current transients at Q1 switching edges.

### 6.3 Ground Plane Continuity

The GND zones on F.Cu and B.Cu were kept continuous. No signal traces were routed through either GND zone in a manner that would interrupt the zone's connectivity. The gate signal trace runs in the lower half of the board, away from the main VIN-DRAIN power path in the upper half, avoiding unintentional coupling between the switching node and the gate drive trace.

### 6.4 Gate Drive and EMI

The 47 Ω series gate resistor (R1) limits the gate current and therefore the rate at which V_GS rises. This slows the switching edge of Q1, reducing the dV/dt at the drain and limiting the high-frequency content of the switching current. For a DC on/off application (not continuous PWM), this is a conservative and appropriate choice. The 100 kΩ pull-down (R2) ensures Q1 remains in the off state during any undefined logic condition, preventing uncontrolled load activation.

The gate drive trace was kept short — R1 is placed directly adjacent to the Q1 gate pin — minimising gate loop inductance. This reduces the risk of oscillatory gate ringing, which can cause unpredictable MOSFET switching behaviour and significant RF emissions.

### 6.5 Thermal Analysis

Power dissipation in Q1 is dominated by conduction loss in R_DS(on). The AO3400A datasheet specifies R_DS(on) ≈ 50 mΩ at V_GS = 4.5 V. Table 4 summarises the computed power dissipation.

**Table 4: Power dissipation calculations**

| Load Current | R_DS(on) | P = I² × R_DS(on) | Comment |
|-------------|----------|-------------------|---------|
| 1.0 A (nominal) | 50 mΩ | 0.05 W | Nominal operating condition |
| 2.0 A (design max) | 50 mΩ | 0.20 W | Maximum design current |

At 0.20 W dissipated in a SOT-23 package (typical θ_JA ≈ 300 °C/W in still air), the theoretical junction temperature rise above ambient is approximately:

```
ΔT_J = P × θ_JA = 0.20 W × 300 °C/W = 60 °C
```

At a 25 °C ambient, this yields a junction temperature of approximately 85 °C, within the AO3400A's rated maximum junction temperature of 150 °C. However, this calculation assumes the worst-case still-air thermal resistance without any PCB copper spreading. In practice, the GND copper zone connected to the Q1 source pad provides significant additional thermal spreading, reducing the effective junction-to-ambient thermal resistance and improving the margin further.

The copper fill on both layers contributes passively to heat spreading from the Q1 source and drain pads. No dedicated thermal vias were added beneath Q1, as the SOT-23 package has no exposed thermal pad; heat is conducted primarily through the drain and source pads to the copper fill.

---

## 7. Manufacturing Output and DFM

### 7.1 Board Outline

The board outline is a closed rectangle on the `Edge.Cuts` layer measuring 39.37 mm × 27.94 mm. The outline was verified to be fully closed with no gaps, as confirmed by passing DRC at final submission. A copper-to-edge clearance of 0.5 mm is maintained throughout.

### 7.2 Design Rules and Clearances

Table 3 (Section 3.3) documents the complete set of design rules applied. The minimum track width of 0.2 mm and minimum clearance of 0.2 mm are consistent with the capabilities of standard PCB fabrication services (e.g., JLCPCB, PCBWay). The actual minimum trace used (0.25 mm signal) exceeds the minimum rule, providing additional fabrication margin.

Drill sizes used are:

- Through-hole connector pads (J1, J2, J3): standard 2.54 mm pitch, hole size per KiCad standard footprint
- Test points (TP1, TP2): 1.0 mm diameter through-hole pad (TestPoint_THTPad_D1.0mm)
- Signal vias: 0.3 mm drill, 0.6 mm diameter
- Power vias: 0.4 mm drill, 0.8 mm diameter

All drill sizes satisfy the 0.3 mm minimum drill rule.

### 7.3 Solder Mask and Silkscreen

Solder mask is applied to both layers, exposing only the pads for soldering. Silkscreen text was reviewed in the DRC and 3D Viewer to confirm no silkscreen text overlaps with pads or component bodies. Reference designator positions were adjusted during the design to clear this requirement (see Section 10, Problem 5).

### 7.4 Gerber File Generation

Gerber files were generated from the KiCad PCB Editor (File → Fabrication Outputs → Gerbers) using the standard KiCad Gerber output settings. The following layers were included:

- F.Cu (front copper)
- B.Cu (back copper)
- F.SilkS (front silkscreen)
- B.SilkS (back silkscreen, blank)
- F.Mask (front solder mask)
- B.Mask (back solder mask)
- Edge.Cuts (board outline)

Excellon drill files were generated alongside the Gerber files.

### 7.5 Gerber Viewer Inspection

The generated Gerber files were inspected in a Gerber viewer prior to submission. The following checks were confirmed to pass:

- Board outline is complete and closed on Edge.Cuts
- Copper pads are present on both F.Cu and B.Cu at expected locations
- Solder mask openings correspond to pad locations
- Silkscreen text does not encroach on pads
- Drill hits correspond to pad centres

### 7.6 Assembly Considerations

All SMD components are on F.Cu only (top side), enabling single-sided SMD assembly. Through-hole connectors (J1, J2, J3) and test points (TP1, TP2) are hand-solderable. D2 (LED, 0805) has a polarity indicator on the silkscreen. J1 Pin 1 (VIN) and J2 Pin 1 (LOAD+) are marked on the silkscreen. The SOT-23 footprint of Q1 includes a pin-1 indicator.

---

## 8. Bill of Materials and Sourcing

Table 6 provides the complete Bill of Materials. Manufacturer Part Numbers (MPNs) are provided where applicable; standard passive components can be sourced from any reputable distributor (Mouser, Digi-Key, LCSC) in the specified package.

**Table 6: Bill of Materials**

| Ref | Description | Value / Spec | Package | MPN (Example) | Qty | Key Rating Justification |
|-----|-------------|--------------|---------|---------------|-----|--------------------------|
| Q1 | N-Ch MOSFET | AO3400A | SOT-23 | AO3400A | 1 | V_DS = 30 V (> 14.4 V min); I_D = 5.7 A; R_DS(on) ≈ 50 mΩ at V_GS = 4.5 V; logic-level threshold |
| R1 | Gate series resistor | 47 Ω | 0603 | Any 47 Ω 1% 0603 | 1 | Limits gate current; reduces switching edge dV/dt; 1/10 W rated; max 5 mA |
| R2 | Gate pull-down | 100 kΩ | 0603 | Any 100 kΩ 1% 0603 | 1 | Holds gate low when SIG is disconnected; negligible DC power |
| R3 | LED current limiter | 3.3 kΩ | 0603 | Any 3.3 kΩ 1% 0603 | 1 | I_LED ≈ 3 mA at 12 V; P = 30 mW < 100 mW rating |
| C1 | Decoupling capacitor | 100 nF | 0603 | Any 100 nF 16 V X7R 0603 | 1 | 16 V rating > 12 V supply; X7R dielectric for stability |
| C2 | Bulk decoupling | 10 µF | 0805 | Any 10 µF 16 V X5R 0805 | 1 | 16 V rating > 12 V supply; bulk energy storage |
| D1 | Flyback diode | SS14 | D_SMA | SS14 (Vishay or equiv.) | 1 | Schottky, 1 A forward, 40 V reverse (> 12 V VIN) |
| D2 | LED indicator | Standard LED | 0805 | Any red/green LED 0805 | 1 | Indicator only; 3 mA operating current |
| J1 | Power input | 1×02 header | PinHeader_1x02_P2.54mm | Generic 2.54 mm header | 1 | Rated ≥ 2 A; standard 2.54 mm pitch |
| J2 | Load output | 1×02 header | PinHeader_1x02_P2.54mm | Generic 2.54 mm header | 1 | Rated ≥ 2 A; standard 2.54 mm pitch |
| J3 | Logic input | 1×02 header | PinHeader_1x02_P2.54mm | Generic 2.54 mm header | 1 | Logic signal only; < 5 mA |
| TP1 | Test point (Drain) | — | TestPoint_THTPad_D1.0mm | Generic TH test point | 1 | 1.0 mm pad for oscilloscope probe |
| TP2 | Test point (GND) | — | TestPoint_THTPad_D1.0mm | Generic TH test point | 1 | GND reference for probe |

**Total unique components:** 13 reference designators, 10 unique part types.

---

## 9. Results and Verification

Table 7 summarises the final verification status of the project.

**Table 7: Final verification results**

| Check | Result | Notes |
|-------|--------|-------|
| Schematic completeness | Pass | All nets connected; all reference designators assigned |
| ERC errors | **0 errors** | Zero unresolved ERC errors at final submission |
| ERC warnings | 2 (acknowledged) | Duplicate net labels on same node (DRAIN/LOAD−, GND/SOURCE) — see Section 10 |
| DRC errors | **0 errors** | Zero unresolved DRC errors at final submission |
| DRC warnings | **0 warnings** | Zero DRC warnings at final submission |
| Board outline | Pass | Closed rectangle on Edge.Cuts; verified by DRC |
| Copper zone fill | Pass | F.Cu and B.Cu GND zones present and filled |
| Trace width — power | Pass | VIN and DRAIN traces: 2.0 mm |
| Trace width — signal | Pass | Gate, SIG, LED: 0.25 mm |
| Test points | Pass | TP1 (Drain), TP2 (GND) |
| 3D Viewer | Pass | Components oriented correctly; silkscreen readable |
| Gerber files | Generated | F.Cu, B.Cu, F.SilkS, F.Mask, B.Mask, Edge.Cuts |
| Excellon drill files | Generated | PTH and SMD drill files |
| BOM | Complete | MPNs included for all active and passive components |

### 9.1 ERC Status

The ERC reported **0 errors**. Two ERC warnings were present at an intermediate stage and were acknowledged:

- Warning: `Both DRAIN and LOAD- are attached to the same items; DRAIN will be used in netlists` — these two labels describe the same physical node (Q1 drain / J2 Pin 2). The warning is correct; both labels are intentionally present for clarity in different parts of the schematic. The final design removed the redundant label to achieve 0 ERC errors.
- Warning: `Both GND and SOURCE are attached to the same items; GND will be used in netlists` — similarly, the SOURCE label and GND symbol are on the same node (Q1 source). The redundant SOURCE label was removed.

These are visible in Figure 2.

### 9.2 DRC Status

The DRC reported **0 errors** and **0 warnings** at final submission. This required resolution of five separate issues encountered during layout, documented in Section 10.

---

## 10. Problems Encountered and Solutions

This section documents all significant design problems encountered during schematic capture and PCB layout, and the engineering solutions applied.

### Problem 1: J3 Logic Input Accidentally Connected to VIN

**Symptom:** During initial schematic capture, J3 Pin 1 was accidentally connected to the VIN net instead of the SIG net. This would have driven the MOSFET gate permanently high regardless of the logic input signal.

**Root cause:** Incorrect net assignment during schematic entry.

**Solution:** The VIN connection was removed from J3 Pin 1 and replaced with a correct connection:

```
J3 Pin 1 → SIG net → R1 (47 Ω) → GATE net → Q1 Gate
```

This was verified by tracing the gate drive path from J3 through R1 to Q1 in the schematic before proceeding to layout.

---

### Problem 2: Net Label Conflicts Causing ERC Warnings

**Symptom:** The ERC reported two warnings regarding duplicate net labels on the same node:
- `DRAIN` and `LOAD−` both assigned to Q1 drain / J2 Pin 2
- `SOURCE` and `GND` both assigned to Q1 source

**Root cause:** The initial schematic used both functional labels (DRAIN, SOURCE) and topology labels (LOAD−, GND symbol) simultaneously on the same nodes, intending to clarify the schematic for the reader. KiCad treats this as a potential conflict.

**Solution:** The redundant labels were removed. Specifically:
- `LOAD−` was removed; `DRAIN` label was retained for the Q1 drain / J2 Pin 2 node
- `SOURCE` label was removed; the GND power symbol alone was used for the Q1 source node

This eliminated the ERC warnings and produced a clean **0-error ERC** result.

---

### Problem 3: Missing GND Connections on F.Cu (DRC Errors)

**Symptom:** The DRC reported multiple "Missing connection between items" errors on GND net pads located on F.Cu. Specific errors included:

- Pad 2 [GND] of C1 on F.Cu ↔ PTH pad 2 [GND] of J1
- Pad 2 [GND] of C2 on F.Cu ↔ Pad 2 [GND] of C1 on F.Cu
- Pad 1 [GND] of TP2 on F.Cu ↔ Pad 2 [GND] of R2 on F.Cu
- Pad 2 [GND] of R2 on F.Cu ↔ Pad 2 [GND] of Q1 on F.Cu

This is visible in Figure 5.

**Root cause:** Only a B.Cu GND copper zone had been created. The SMD components (C1, C2, R2, Q1, TP2) all have GND pads on F.Cu. Since there was no copper on F.Cu connecting these pads, the DRC correctly reported them as unconnected — despite all being on the same net.

**Solution:** A second GND copper zone was added on F.Cu. With both F.Cu and B.Cu GND zones filled and connected to the GND net, all SMD GND pads on the component side were connected by copper, and all DRC errors in this category were resolved.

**Lesson:** For any 2-layer design with SMD components on the top layer, a GND copper zone must exist on F.Cu as well as B.Cu. Relying solely on the bottom copper plane leaves top-layer SMD pads unconnected unless explicit traces or vias are routed.

---

### Problem 4: Board Outline Error — No Edges on Edge.Cuts

**Symptom:** The DRC reported: "Board has malformed outline (no edges found on Edge.Cuts layer)."

**Root cause:** The board outline had not yet been drawn; the Edge.Cuts layer was empty.

**Solution:** A closed rectangular board outline measuring 39.37 mm × 27.94 mm was drawn on the Edge.Cuts layer using the KiCad PCB Editor's rectangle tool. The DRC board-outline check passed after this was completed.

---

### Problem 5: Silkscreen Clearance Warnings

**Symptom:** The DRC reported silkscreen clearance warnings where reference designator text or value text overlapped component pads or the component courtyard.

**Root cause:** Default reference designator positions placed text directly over or adjacent to pads, particularly for small SMD components such as 0603 resistors and the SOT-23 MOSFET.

**Solution:** All reference designators and value labels were manually repositioned to clear pad areas and courtyards. Text was oriented horizontally where possible for readability. After repositioning, the DRC reported **0 warnings** related to silkscreen.

---

## 11. Discussion

### 11.1 Successful Design Decisions

The placement strategy — left-to-right power flow with decoupling capacitors immediately at the supply entry — resulted in a short, direct high-current path. The VIN trace from J1 to J2 spans the full width of the board but is wide (2.0 mm) and avoids any unnecessary bends that would add resistance or inductance. The DRAIN trace from J2 to Q1 is short, keeping the inductive load loop compact.

The decision to include a flyback diode (D1) even for nominally resistive loads is conservative but correct: if the load type is changed during service (e.g., a relay or motor is connected), the board will not be damaged by the inductive transient. This adds a single low-cost SS14 component.

The LED indicator circuit provides a useful visual cue during commissioning. Because it is connected to the DRAIN node rather than the gate, it accurately reflects the actual switched state of the load, not merely the logic input level.

### 11.2 Problems and Their Impact

The most consequential design problem was Problem 3 (missing GND connections on F.Cu). This error would have produced a completely non-functional board: Q1 source, C1 negative, C2 negative, and R2 ground would all have been floating, making the circuit unable to switch at all. Identifying this through DRC — rather than discovering it after assembly — demonstrates the importance of running DRC with zone refill before generating manufacturing files.

Problem 1 (J3 connected to VIN) would also have produced a non-functional board in a different mode: Q1 would have been permanently on, and the load would have been permanently connected regardless of the logic input. This was caught during schematic review before layout began.

### 11.3 Compromises

The SOT-23 package for Q1 was selected for compactness. At the nominal 1 A load, the power dissipation is only 50 mW, well within SOT-23 capability. At the 2 A design maximum, the theoretical junction temperature rise is approximately 60 °C (with still-air θ_JA), yielding a junction temperature of approximately 85 °C — within specification but with reduced margin. A future revision targeting sustained 2 A operation in a high-ambient environment might consider a larger package such as SOT-223 or DPAK for improved thermal performance.

The use of standard 2.54 mm pin headers (J1, J2, J3) rather than screw terminals limits the maximum current rating of the connectors. For applications near the 2 A design limit, screw terminals or heavier-duty connectors would be preferable.

### 11.4 Potential Future Improvements

- **TVS diode on LOAD+ to GND:** For applications with long inductive loads or harsh environments, a TVS clamp on the supply rail would provide additional protection against supply transients.
- **RC snubber across D1:** For very fast-switching applications, an RC snubber would further reduce DRAIN node voltage ringing at turn-off.
- **Screw terminal connectors for J1 and J2:** Replacing pin headers with screw terminals would improve mechanical robustness for the power connections.
- **Thermal vias beneath Q1:** Although the SOT-23 has no exposed pad, adding copper pours and vias in the drain and source copper areas would improve heat transfer to the B.Cu GND plane.
- **Additional test point on GATE net:** A test point on the GATE net (currently only accessible via the R1 pad) would facilitate measurement of gate voltage waveform without risk of touching adjacent signal traces.

---

## 12. Conclusion

The Low-Side MOSFET Load Driver PCB was designed, verified, and prepared for manufacturing within the constraints of the project specification. The board measures 39.37 mm × 27.94 mm on a 2-layer FR4 substrate (1.6 mm, 1 oz copper) and implements a complete logic-controlled DC load switch using an AO3400A N-channel MOSFET.

All design objectives were met:

- The circuit correctly implements a low-side switch for 12 V loads up to 1 A nominal (2 A design margin).
- Power traces are sized to 2.0 mm for the design current of 2 A.
- Gate drive and pull-down networks are correctly implemented.
- A flyback diode protects against inductive load transients.
- Dual GND copper zones on F.Cu and B.Cu provide continuous ground referencing.
- Two test points (TP1 on DRAIN, TP2 on GND) enable in-circuit measurement.
- The design achieved **0 ERC errors**, **0 DRC errors**, and **0 DRC warnings** at final submission.
- A complete manufacturing package was generated: Gerber files, Excellon drill files, BOM, schematic PDF, and 3D Viewer screenshots.

The most important technical lesson from this project is that a GND copper zone must be present on every copper layer that contains SMD GND pads — not merely on the layer opposite the components. Relying on a single-layer GND plane produces correct-looking airwire clearance in KiCad's unrouted view, but the DRC correctly flags the missing copper connections once zone fill is run.

The design is manufacturable using standard two-layer PCB fabrication processes and is documented sufficiently for another engineer to review, build, and test without requiring additional clarification.

---

## 13. References

[1] Alpha & Omega Semiconductor, *AO3400A — N-Channel Enhancement Mode Field Effect Transistor Datasheet*, latest available revision. [Online]. Available: https://www.aosmd.com/

[2] Vishay Semiconductors, *SS14 — Surface Mount Schottky Barrier Rectifier Datasheet*, latest available revision. [Online]. Available: https://www.vishay.com/

[3] IPC, *IPC-2152 — Standard for Determining Current Carrying Capacity in Printed Board Design*, IPC International.

[4] KiCad Documentation Team, *KiCad EDA Documentation*, available through the official KiCad documentation pages. [Online]. Available: https://docs.kicad.org/

[5] SRH University of Applied Sciences, *PCB Design and Layout — Final Exam Brief*, Electronic Development Module, Prof. Dr. Oezdemir Cetin.

[6] Component manufacturers, *Relevant component datasheets and application notes for SS14, 0603/0805 passive components*, latest available revisions.

---

*End of Report*
