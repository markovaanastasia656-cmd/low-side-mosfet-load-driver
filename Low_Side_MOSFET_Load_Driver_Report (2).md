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
| Figure 3 | Intermediate DRC result showing GND missing-connection errors on F.Cu | `スクリーンショット_2026-05-31_015000.png` |
| Figure 4 | Intermediate PCB layout state that produced the DRC errors in Figure 3 | `スクリーンショット_2026-05-31_015135.png` |
| Figure 5 | Final PCB layout with board outline and complete routing | `スクリーンショット_2026-05-31_012622.png` |
| Figure 6 | 3D Viewer — top view of the fully populated PCB | `スクリーンショット_2026-05-31_062012.png` |
| Figure 7 | 3D Viewer — bottom view showing through-hole connector and test-point pins | `スクリーンショット_2026-05-31_062022.png` |

> **Note on figure selection:** Only figures providing meaningful technical evidence are included. Screenshots of routine KiCad settings panels (Design Rules Constraints, Pre-defined Sizes) are referenced in tables (Tables 3 and 4) rather than as figures, since the tables capture the same information more concisely.

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

The entire design was completed using KiCad (version 8.x). The schematic was drawn in the KiCad Schematic Editor; the PCB layout was developed in the KiCad PCB Editor. The 3D Viewer was used for mechanical and visual inspection (Figures 6 and 7). Electrical and design rule checks were performed using the integrated ERC and DRC engines.

### 3.2 PCB Technology Assumptions

The board was designed to the following stackup and fabrication parameters:

- Substrate: FR4
- Layers: 2 (F.Cu — component/top side; B.Cu — bottom side)
- Board thickness: 1.6 mm
- Copper weight: 1 oz (35 µm)
- Solder mask: Both sides
- Silkscreen: F.Cu (top side)

### 3.3 Design Rules

The KiCad Board Setup (Design Rules → Constraints) was configured as documented in Table 3. The values selected are consistent with the capabilities of standard two-layer PCB fabrication services.

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

Two track widths and two via sizes were configured in KiCad's Pre-defined Sizes panel and applied consistently throughout routing. These are summarised in Table 4.

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
> <img width="1422" height="598" alt="スクリーンショット 2026-05-30 122322" src="https://github.com/user-attachments/assets/20d4cdde-d853-4f50-b361-1728b7d6896c" />


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

The indicator circuit consists of R3 (3.3 kΩ, `R_0603_1608Metric`) and D2 (`LED_SMD:LED_0805_2012Metric`), connected in series between the DRAIN net and GND.

The purpose of the indicator is to provide a visual indication of the voltage present at the MOSFET drain node. When Q1 is turned on, the drain is pulled close to ground potential and little or no voltage appears across the LED circuit, causing D2 to remain off.

When Q1 is turned off and a load is connected between VIN and the DRAIN node, the drain voltage rises toward VIN through the load. Under these conditions, current flows through R3 and D2, causing the LED to illuminate.

Therefore, the LED acts as an indicator of drain voltage rather than a direct indicator of gate-drive status. This approach provides additional diagnostic value because it reflects the actual state of the switching node rather than merely indicating the logic signal applied to the gate.

For a 12 V supply and an LED forward voltage of approximately 2.0 V:

```text
I_LED = (V_DRAIN - V_f) / R3
      = (12 V - 2.0 V) / 3300 Ω
      ≈ 3.0 mA
```

Power dissipation in R3 is:

```text
P_R3 = I²R
     = (0.003 A)² × 3300 Ω
     ≈ 0.030 W
```

This is well below the typical 0603 resistor power rating of 0.1 W.

### 4.8 Test Points

Two test points using footprint `TestPoint_THTPad_D1.0mm` are included:

- **TP1** — connected to the DRAIN net. This is the primary switching node and the most diagnostic point for functional verification with an oscilloscope.
- **TP2** — connected to GND. Providing a GND reference adjacent to TP1 allows reliable oscilloscope probing without hunting for a remote ground connection.

---

## 5. PCB Layout Design

### 5.1 Board Outline and Mechanical Constraints

The board outline is a closed rectangle on the `Edge.Cuts` layer measuring **39.37 mm × 27.94 mm**, visible in Figures 5, 6, and 7. The outline was verified to be fully closed by the DRC board-outline check at final submission. A copper-to-edge clearance of 0.5 mm is maintained throughout, consistent with the design rules documented in Table 3.
**Figure 5**
<img width="1034" height="697" alt="スクリーンショット 2026-05-31 015135" src="https://github.com/user-attachments/assets/ec5f8393-b40a-4153-aba1-9bcd54cf0ac1" />


### 5.2 Component Placement Strategy

Components were placed according to an intended left-to-right power-flow strategy, following the signal and current path from power input through decoupling and load switching to the drain output and gate drive circuits. This is visible in the final PCB layout (Figure 5) and confirmed by the 3D Viewer (Figures 6 and 7).

As visible in Figure 6 (3D top view), the placement groups are:

- **Left side:** J1 (power input connector), C1 and C2 (decoupling capacitors placed adjacent to J1)
- **Upper centre:** D1 (SS14 flyback diode, SMA package — visible as the large SMD body)
- **Upper right:** J2 (load output connector)
- **Centre:** Q1 (AO3400A SOT-23), R1 (47 Ω gate series), R2 (100 kΩ gate pull-down)
- **Lower centre-left:** J3 (logic input connector)
- **Right side:** D2 (LED indicator, 0805), R3 (3.3 kΩ, 0603), TP1 (Drain test point)
- **Lower centre:** TP2 (GND test point)

The silkscreen labels visible in Figure 6 confirm the net names at key connector pins: VIN, GND, LOAD+, LOAD−, SIG, GATE, DRAIN, confirming correct orientation and connectivity.

### 5.3 Routing Strategy

**High-current traces (VIN, DRAIN):** Power traces were routed at **2.0 mm** width. As visible in Figure 4 (intermediate PCB layout) and Figure 5 (final layout), the F.Cu traces for VIN and DRAIN are clearly wider than the signal traces running to the gate network. The VIN trace routes across the upper portion of the board from J1 to J2. The DRAIN trace connects J2 Pin 2 to the drain pad of Q1.

**Signal traces (GATE, SIG, LED path):** Signal traces were routed at **0.25 mm** width. The SIG trace from J3 to R1, the GATE trace from R1 to the Q1 gate, the R2 pull-down connection, and the LED indicator path all use 0.25 mm width. These nets carry less than 5 mA.

**Vias:** Via sizes used are 0.6 mm diameter / 0.3 mm hole for signal vias and 0.8 mm diameter / 0.4 mm hole for power vias, consistent with the minimum drill rule of 0.3 mm.

### 5.4 Ground Plane Implementation

GND copper zones were applied to both F.Cu and B.Cu layers, both assigned to the GND net. The F.Cu GND zone fills the component-side copper between and around the SMD pads. The B.Cu GND zone provides the primary return plane on the bottom layer. The importance of including the F.Cu zone specifically — and the consequences of omitting it — are documented in detail in Section 10 (Problem 1).

The final layout with zone fill is visible in Figure 5, where the dark blue background represents the board outline and the B.Cu copper zone fill. The intermediate state before the F.Cu zone was added is visible in Figure 4, where the red (F.Cu) copper fill is shown and the GND connections on the top layer were incomplete.

### 5.5 Silkscreen

Reference designators and net labels are included on the F.Cu silkscreen. As confirmed by the 3D Viewer top view (Figure 6), all labels are readable and positioned clear of pad areas and courtyard boundaries. Reference designator positions were adjusted during the design iteration to resolve silkscreen clearance warnings encountered during DRC.

### 5.6 3D Viewer Inspection

Figure 6 shows the top view from the KiCad 3D Viewer. All SMD components are visible on the top surface. The SOT-23 package (Q1), the SMA diode (D1), the 0805 capacitors and LED, the 0603 resistors, and the pin headers are all present and oriented consistently with the schematic. The test points TP1 and TP2 are visible as yellow pads. The board outline and GND copper fill are confirmed by the board shape.

Figure 7 shows the bottom view, confirming that the through-hole connector legs (J1, J2, J3) and test point pins protrude through the board and are available for soldering.
**Figure 6**
<img width="1020" height="751" alt="スクリーンショット 2026-05-31 062012" src="https://github.com/user-attachments/assets/99108505-532c-4583-a931-2396b213c7aa" />
**Figure 7**
<img width="852" height="641" alt="スクリーンショット 2026-05-31 062022" src="https://github.com/user-attachments/assets/781db0c6-1dea-4f74-8a97-71dee9621af5" />



---

## 6. EMC, Grounding, and Thermal Considerations

### 6.1 Decoupling Capacitor Placement

C1 (100 nF, `C_0603_1608Metric`) and C2 (10 µF, `C_0805_2012Metric`) are placed close to the power input connector J1, as shown in Figures 5 and 6. Locating the decoupling network near the supply entry point reduces the impedance between the external supply and the local PCB power distribution network.

The 100 nF ceramic capacitor provides high-frequency decoupling, while the 10 µF capacitor provides local bulk energy storage. Together, they help maintain a stable supply voltage during load-switching events and reduce sensitivity to supply-cable inductance.

### 6.2 Return Current Path and Loop Area Minimisation

The primary load-current path is:

```text
VIN (J1 Pin 1)
   → J2 Pin 1 (LOAD+)
   → External Load
   → J2 Pin 2 (LOAD− / DRAIN)
   → Q1 Drain
   → Q1 Source
   → GND Network
   → J1 Pin 2
```

As shown in Figure 5, GND copper zones are present on both F.Cu and B.Cu layers. These copper areas provide a low-impedance return path for load current and help reduce unnecessary current-loop area within the PCB.

Reducing loop area lowers parasitic inductance and helps minimise electromagnetic emissions generated during switching transitions.

### 6.3 Ground Plane Implementation

A GND copper zone is implemented on both F.Cu and B.Cu and assigned to the GND net. The final implementation is shown in Figure 5.

During development, an earlier PCB revision contained only a B.Cu GND zone. As discussed in Section 10, this resulted in several GND-connected pads on F.Cu remaining electrically unconnected, which was detected by DRC. The final design resolves this issue by providing GND copper on both layers.

The dual-layer ground implementation improves electrical connectivity, simplifies routing, and provides additional copper area for current return paths.

### 6.4 Gate Drive and EMI Considerations

The MOSFET gate is driven through R1 (47 Ω), which is connected in series between the SIG input and the AO3400A gate.

The resistor limits instantaneous gate-charging current and reduces the edge rate of the gate voltage transition. Although the circuit is intended for simple on/off load switching rather than high-frequency PWM operation, controlling switching-edge speed can help reduce high-frequency noise generation.

R2 (100 kΩ) provides a pull-down path from the gate to GND. This ensures that Q1 remains turned off whenever the logic input is disconnected or left floating.

As shown in Figures 5 and 6, R1 is placed close to the MOSFET gate connection, keeping the gate-routing length short and reducing susceptibility to unwanted noise pickup.

### 6.5 Thermal Analysis

The dominant power loss in Q1 is MOSFET conduction loss, which can be estimated from:

P = I² × R_DS(on)

Using the AO3400A typical on-resistance of approximately 50 mΩ at logic-level gate drive, the estimated conduction losses are shown in Table 5.

**Table 5: Estimated MOSFET conduction loss**

| Load Current | Assumed R_DS(on) | Power Dissipation |
|-------------|------------------|------------------|
| 1.0 A | 50 mΩ (typ.) | 0.05 W |
| 2.0 A | 50 mΩ (typ.) | 0.20 W |

A simplified thermal estimate can be obtained using:

```text
ΔT_J = P × θ_JA
```

Assuming:

```text
P = 0.20 W
θ_JA ≈ 100–300 °C/W
```

the estimated junction-temperature rise is:

```text
ΔT_J ≈ 20–60 °C
```

The actual thermal performance depends strongly on PCB copper area, airflow, mounting conditions, and operating duty cycle. Because the AO3400A datasheet specifies different thermal resistances for different PCB conditions, the calculation above should be regarded as an engineering estimate rather than a guaranteed worst-case result.

As shown in Figure 5, the MOSFET is connected to relatively large copper areas through the source and drain connections. These copper regions provide additional heat spreading and are expected to reduce the effective thermal resistance compared with a minimal-footprint SOT-23 installation.

**Table 6: Trace Width Design Summary**

| Net | Design Current | Selected Width | Rationale |
|------|------|------|------|
| VIN | 2.0 A | 2.0 mm | IPC-2152 guidance for 1 oz copper |
| DRAIN | 2.0 A | 2.0 mm | Main load-current path |
| GATE | < 5 mA | 0.25 mm | Logic signal |
| SIG | < 5 mA | 0.25 mm | Logic input |
| LED path | ~3 mA | 0.25 mm | Indicator current only |

## 7. Manufacturing Output and DFM

### 7.1 Board Outline

The board outline is a closed rectangle on the `Edge.Cuts` layer measuring 39.37 mm × 27.94 mm, confirmed by the DRC board-outline check at final submission (see Section 9). The copper-to-edge clearance of 0.5 mm is maintained throughout.

### 7.2 Design Rules and Clearances

The design rules are documented in Table 3. The actual minimum track width used (0.25 mm for signals, 2.0 mm for power) exceeds the 0.2 mm rule minimum, providing fabrication margin. The minimum drill size used is 0.3 mm for signal vias, consistent with standard two-layer PCB services.

Drill sizes used in the design:

- Signal vias: 0.3 mm drill, 0.6 mm pad diameter
- Power vias: 0.4 mm drill, 0.8 mm pad diameter
- Test points (TP1, TP2): 1.0 mm through-hole pad (`TestPoint_THTPad_D1.0mm`)
- Through-hole connector pads (J1, J2, J3): standard 2.54 mm pitch per `PinHeader_1x02_P2.54mm_Vertical` footprint

### 7.3 Solder Mask and Silkscreen

Solder mask is applied to both layers. Silkscreen text was reviewed using the DRC and 3D Viewer to confirm no text overlaps with pads or component bodies. Reference designator positions were adjusted during layout to resolve silkscreen clearance warnings identified by the DRC.

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

All SMD components are on F.Cu only, enabling single-sided SMD assembly. Through-hole connectors (J1, J2, J3) and test points (TP1, TP2) are hand-solderable from the bottom side, as confirmed by the 3D bottom view in Figure 7. D2 (LED, 0805) has polarity indicated by the silkscreen. J1 Pin 1 (VIN) and J2 Pin 1 (LOAD+) are labelled on the silkscreen, as visible in Figure 6.

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
| GND copper zones | Pass | F.Cu and B.Cu zones present (Figures 4, 5) |
| Power trace width | Pass | VIN and DRAIN traces: 2.0 mm (Figures 4, 5) |
| Signal trace width | Pass | GATE, SIG, LED: 0.25 mm |
| Test points | Pass | TP1 (Drain), TP2 (GND) — visible in Figure 6 |
| 3D Viewer check | Pass | Figures 6 and 7 — components oriented correctly |
| Gerber files | Generated | F.Cu, B.Cu, F.SilkS, F.Mask, B.Mask, Edge.Cuts |
| Excellon drill files | Generated | PTH drill file included |
| BOM | Complete | Table 7; MPNs included for all components |

### 9.1 ERC Status — Net Label Warnings

The ERC, shown in Figure 2, reports **0 errors** and **2 warnings**. Both warnings arise from the same cause: KiCad detects that two different labels are attached to the same net node and notifies the designer which name will take precedence in the netlist.

**Figure 2**
<img width="1237" height="609" alt="スクリーンショット 2026-05-30 122352" src="https://github.com/user-attachments/assets/ca5ce4cb-b1c2-4140-8508-9ffbbd8d4736" />


The two warnings are:

1. **`Both DRAIN and LOAD- are attached to the same items; DRAIN will be used in netlists`**  
   The node at Q1 drain / J2 Pin 2 carries both the `DRAIN` net label and the `LOAD−` net label. Both are intentionally present — `LOAD−` describes the connector function while `DRAIN` describes the MOSFET node — but they refer to the same copper node. KiCad reports this as a warning, not an error, since both labels are electrically correct. The ERC engine will use `DRAIN` as the canonical net name in any exported netlist.

2. **`Both GND and SOURCE are attached to the same items; GND will be used in netlists`**  
   The node at Q1 source carries both the GND power symbol and a `SOURCE` net label. This is again intentional: `SOURCE` was added for schematic readability. KiCad correctly identifies these as the same node and will use `GND` as the canonical name.

These warnings are acknowledged and accepted. No functional or manufacturing consequence results from either warning, as both cases describe correct electrical connections. The ERC reports **0 errors**.

---

## 10. Problems Encountered and Solutions

### 10.1 Design Iteration and Debugging Process

The design was developed iteratively, with ERC and DRC run at each significant milestone. The key stages were:

1. **Schematic capture and ERC** — The schematic was drawn and the ERC was run to verify net connectivity. An ERC error was raised because J3's GND pin lacked a connected `PWR_FLAG` symbol (Problem 1 below). This was resolved before layout began.

2. **Component placement** — All 13 components were imported into the PCB editor and placed according to the intended left-to-right power-flow strategy.

3. **Trace routing** — VIN and DRAIN power traces were routed at 2.0 mm; gate, signal, and indicator traces at 0.25 mm. GND copper zones were initially applied to B.Cu only.

4. **First DRC run — GND connectivity errors identified** — Running DRC with zone refill enabled revealed multiple missing-connection errors on F.Cu GND pads (Problem 2 below). A second GND zone was added to F.Cu to resolve these.

5. **Final DRC and ERC** — All checks confirmed: 0 DRC errors, 0 DRC warnings, 0 ERC errors (2 acknowledged net-label warnings).

6. **3D Viewer inspection** — Final visual check of component orientations, silkscreen readability, and board shape (Figures 6 and 7).

---

### Problem 1: ERC Error — Missing PWR_FLAG on the GND Net

#### Symptom

During an early ERC run, KiCad reported an ERC error indicating that an input power pin was not driven by any output power pin.

Although the GND symbols were electrically connected throughout the schematic, the ERC engine could not identify any recognised power source driving the GND net. As a result, the ERC generated an "Input Power pin not driven by any Output Power pins" error and the design could not achieve a clean ERC result.

#### Root Cause

KiCad's ERC system distinguishes between ordinary electrical connections and nets that are recognised as valid power sources.

In the initial version of the schematic, a PWR_FLAG symbol had been placed only on the VIN net near connector J1. No PWR_FLAG was connected to the GND net.

Although the GND symbols visually connected all ground nodes together, KiCad treats power symbols such as GND as power-input pins rather than power sources. Because the ERC engine could not identify any component explicitly driving the GND net, it treated the associated power-input pins as undriven and generated an ERC violation.

The reported message was:

Input Power pin not driven by any Output Power pins

This behaviour is common in KiCad designs where power is supplied through external connectors rather than through components that KiCad recognises as power-output devices. In such cases, a PWR_FLAG symbol is required to inform the ERC engine that the net is intentionally powered by an external source.

#### Solution

A PWR_FLAG symbol was added to the GND net to indicate that the ground network is supplied by an external source and should be treated as a valid power net by the ERC engine.

After adding the `PWR_FLAG`, the ERC engine recognised the GND network as a valid externally supplied return path and the power-pin error disappeared.

The final schematic shown in Figure 1 contains:

- one `PWR_FLAG` on the VIN net,
- one `PWR_FLAG` on the GND net.

After re-running ERC, the design achieved the final result shown in Figure 2:

- 0 ERC errors
- 2 acknowledged net-label warnings

#### Key Lesson

When a design receives power from external connectors rather than from explicit power-source components, both the positive supply rail and the return rail should be identified to the ERC engine.

In KiCad, this is commonly achieved by placing `PWR_FLAG` symbols on the externally supplied power nets. A PWR_FLAG on the positive supply rail alone may not be sufficient. Any externally supplied power net that is interpreted as a power-input network by ERC, including the return (GND) rail, must be recognised by the ERC engine to avoid false power-connectivity errors.

---

### Problem 2: Missing GND Connections on F.Cu (DRC Errors)

**Symptom:** During PCB verification, the Design Rule Check (DRC) reported multiple **"Missing connection between items"** errors involving pads assigned to the GND net. As shown in Figure 3, the reported errors included missing copper connections between several GND-connected pads on the top copper layer (F.Cu).
**Figure 3**
<img width="809" height="721" alt="スクリーンショット 2026-05-31 015000" src="https://github.com/user-attachments/assets/08e6cf50-8eca-4b30-b5f6-1cd8ba83e615" />


Examples reported by DRC included:

- C1 Pad 2 (GND) ↔ J1 Pad 2 (GND)
- C2 Pad 2 (GND) ↔ C1 Pad 2 (GND)
- TP2 Pad 1 (GND) ↔ R2 Pad 2 (GND)
- R2 Pad 2 (GND) ↔ Q1 Source Pad (GND)

Figure 4 shows the PCB layout at the stage when these errors were generated. Although the pads were correctly assigned to the GND net in the schematic and netlist, DRC detected that no valid copper connection existed between several of the associated pads.
**Figure 4**
<img width="1039" height="710" alt="スクリーンショット 2026-05-31 012622" src="https://github.com/user-attachments/assets/5d604998-b782-4ddc-a777-2b02d3f5e552" />


**Root Cause:** At this stage of the design, a GND copper zone had been created only on the B.Cu layer. Several GND-connected pads were located on F.Cu and were not connected by traces, vias, or an F.Cu copper zone. As a result, these pads belonged to the same logical net but were not physically connected in the PCB layout.

KiCad correctly identified these missing physical connections and reported them as DRC errors. This issue illustrates the distinction between logical connectivity defined by the schematic and physical connectivity implemented in the PCB layout.

**Solution:** To resolve the issue, an additional GND copper zone was created on the F.Cu layer and assigned to the GND net. After executing **Fill All Zones**, the F.Cu copper zone established the required physical connections between the affected GND pads.

Following the zone refill, all reported missing-connection errors were cleared successfully. The corrected implementation is shown in Figure 5, where GND copper zones are present on both F.Cu and B.Cu.

**Key Lesson:** DRC verification is essential whenever copper zones are used as part of the electrical connectivity strategy. Components that appear connected in the schematic may remain physically disconnected in the PCB layout if copper zones, traces, or vias are not implemented correctly.

In this project, the issue was identified because DRC was run with **"Refill all zones before performing DRC"** enabled (Figure 3). This setting ensures that copper-zone connectivity is evaluated using the latest zone geometry and helps reveal connectivity problems that might otherwise be overlooked during layout inspection.

---

## 11. Discussion

### 11.1 Successful Design Decisions

The final component placement, shown in Figures 5 and 6, follows a logical current-flow arrangement from the power input connector (J1) through the switching stage (Q1) to the load connector (J2). This organisation simplifies routing, reduces unnecessary trace length in the main power path, and improves readability during both design review and troubleshooting.

The placement of the decoupling capacitors (C1 and C2) adjacent to the power input connector J1 is another beneficial design decision. By locating the capacitors close to the supply entry point, transient current demands can be supported locally, reducing the influence of supply-cable impedance and helping to maintain a stable VIN rail during switching events.

As visible in Figures 5 and 6, the inclusion of the SS14 flyback diode (D1) across the load connector improves robustness when inductive loads are connected. Because the board is intended for use with a variety of external loads, including small DC motors and other inductive devices, integrating the flyback protection directly on the PCB reduces the risk of voltage transients appearing at the MOSFET drain.

The LED indicator connected to the DRAIN node provides information about the voltage present at the switching node rather than simply indicating the logic input state. During testing and debugging, this arrangement can provide additional information about circuit operation and may help identify discrepancies between the commanded gate signal and the observed drain voltage.

The inclusion of TP1 (DRAIN) and TP2 (GND), shown in Figure 6, improves measurement accessibility. The test points provide convenient locations for oscilloscope probes and multimeter measurements without requiring direct access to component leads or connector pins.

### 11.2 Problems and Their Design Impact

Two significant issues were identified and resolved during the design process.

The first issue was an ERC error related to power-net definition. Initially, a PWR_FLAG symbol was placed only on the VIN net, while the GND net contained only passive connections and power-input symbols. As a result, ERC reported the message *"Input Power pin not driven by any Output Power pins"* because KiCad could not identify a recognised power source associated with the ground network.

The issue was resolved by adding a PWR_FLAG symbol to the GND net, allowing ERC to recognise the ground network as a valid power-return connection. After this modification, ERC completed successfully with zero errors. Although the issue did not affect PCB manufacturability directly, it highlighted the importance of correctly defining power networks so that schematic verification can be completed successfully.

The second issue involved missing GND connectivity on the F.Cu layer. As shown in Figure 3, DRC reported multiple missing-connection errors associated with pads assigned to the GND net. Investigation revealed that only a B.Cu GND zone had been created at that stage of development. Consequently, several GND-connected pads located on F.Cu were not physically connected to the intended ground network.

The problem was resolved by adding a GND copper zone on F.Cu and refilling all copper zones. The corrected implementation is visible in Figure 5. Without DRC verification, the resulting PCB would have contained electrically isolated ground connections, preventing correct circuit operation.

Together, these issues demonstrate the complementary roles of ERC and DRC. ERC identified a schematic-level power-network definition problem, whereas DRC detected a physical connectivity problem that was not immediately obvious from visual inspection of the PCB layout.

### 11.3 Compromises and Limitations

The AO3400A MOSFET was selected in a SOT-23 package to maintain a compact PCB footprint. As discussed in Section 6.5, estimated conduction losses remain relatively low for the intended operating range. However, thermal performance in small packages depends strongly on PCB copper area, ambient temperature, airflow, and operating conditions. For applications requiring sustained operation near the upper current limit, a larger package such as SOT-223 or DPAK would provide additional thermal margin.

The design uses standard 2.54 mm pin-header connectors for J1 and J2. These connectors are inexpensive, widely available, and suitable for laboratory use and prototype evaluation. Nevertheless, screw-terminal connectors would provide improved mechanical robustness and may be preferable for applications involving frequent cable installation or higher continuous currents.

Another limitation is that the project focused on schematic development, PCB layout, design verification, and manufacturing-file generation. While ERC, DRC, 3D inspection, and manufacturing-output checks were completed successfully, no fabricated prototype was available during the project. Consequently, electrical performance under real operating conditions remains to be verified experimentally.

### 11.4 Potential Future Improvements

- **Screw-terminal connectors for J1 and J2:** Improve mechanical robustness and current-handling capability for power connections.

- **Additional GATE test point:** A dedicated test point on the GATE net would simplify oscilloscope measurements of the MOSFET gate waveform.

- **Larger MOSFET package:** Replacing the SOT-23 package with SOT-223 or DPAK would provide additional thermal margin for sustained high-current operation.

- **Transient-voltage-suppression (TVS) protection:** A TVS diode across the VIN input could improve resilience against supply-line voltage spikes.

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

The two most important technical lessons from this project address different stages of the design process. At the schematic level: every connector that independently brings a power or ground net into the schematic requires its own `PWR_FLAG` symbol — a single flag at the main supply connector does not satisfy the ERC for subsequent connector GND pins. At the PCB layout level: a GND copper zone must be present on every copper layer that contains SMD GND pads. A single bottom-layer ground plane leaves all top-side SMD GND pads electrically unconnected in copper — a failure mode invisible to visual inspection but reliably detected by running DRC with zone refill enabled.

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

The following table maps each figure in this report to the exact screenshot filename from the project archive. Screenshots of routine KiCad settings panels are documented in Tables 3 and 4 rather than as figures.

| Figure | Screenshot Filename | Content |
|--------|--------------------|---------| 
| Figure 1 | `スクリーンショット_2026-05-31_061710.png` | Final schematic (KiCad Schematic Editor) |
| Figure 2 | `スクリーンショット_2026-05-31_061749.png` | ERC result: 0 errors, 2 acknowledged warnings |
| Figure 3 | `スクリーンショット_2026-05-31_015000.png` | Intermediate DRC: GND missing-connection errors on F.Cu |
| Figure 4 | `スクリーンショット_2026-05-31_015135.png` | Intermediate PCB layout (B.Cu zone only, pre-F.Cu zone) |
| Figure 5 | `スクリーンショット_2026-05-31_012622.png` | Final PCB layout with board outline and complete routing |
| Figure 6 | `スクリーンショット_2026-05-31_062012.png` | 3D Viewer — top view of the populated PCB |
| Figure 7 | `スクリーンショット_2026-05-31_062022.png` | 3D Viewer — bottom view, through-hole pins visible |

---

*End of Report*
