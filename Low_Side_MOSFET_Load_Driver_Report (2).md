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

Connector J1 (`PinHeader_1x02_P2.54mm_Vertical`) provides the 12 V supply. Pin 1 is connected to the VIN net; Pin 2 is connected to GND.

Two decoupling capacitors — C1 (100 nF, `C_0603_1608Metric`) and C2 (10 µF, `C_0805_2012Metric`) — are connected in parallel between VIN and GND.

C1 provides high-frequency decoupling for fast transient currents generated during switching events. A value of 100 nF is widely used for local bypassing because it maintains a low impedance at high frequencies while remaining small enough to minimise parasitic inductance. Smaller values such as 10 nF provide less charge storage, while larger values such as 1 µF generally offer little additional benefit for high-frequency decoupling.

C2 provides bulk energy storage for lower-frequency load transients. A value of 10 µF was selected to provide a local energy reservoir capable of supplying transient load current without excessive VIN voltage droop. Smaller values would reduce transient energy storage, while significantly larger values would increase component size with limited benefit for the intended 0.5–1.0 A operating range.

Together, C1 and C2 provide broadband supply decoupling, with C1 addressing high-frequency switching noise and C2 supporting lower-frequency current demand.

### 4.3 Load Connector and Flyback Diode

Connector J2 (`PinHeader_1x02_P2.54mm_Vertical`) is the load output terminal. Pin 1 is connected to the VIN net (labelled LOAD+); Pin 2 is connected to the DRAIN net (labelled LOAD−). The load is therefore connected in series with the drain of Q1 between VIN and GND, implementing the low-side switching topology.

Diode D1 (SS14, `Diode_SMD:D_SMA`) is connected across the load connector as a flyback clamp. The cathode of D1 connects to LOAD+ (VIN); the anode connects to LOAD− (DRAIN).

When Q1 turns off, inductive energy stored in any reactive load forces current to continue flowing. Without D1, the drain voltage could rise well above the supply voltage and potentially exceed the MOSFET drain-source rating. The flyback diode provides an alternate current path, allowing the inductive energy to recirculate safely until it dissipates.

The SS14 was selected because its Schottky construction provides fast response and a low forward voltage drop (typically approximately 0.4 V). During turn-off of an inductive load, the drain voltage is therefore limited to approximately:

```text
VPEAK ≈ VIN + VF
      ≈ 12 V + 0.4 V
      ≈ 12.4 V
```

This remains well below the AO3400A drain-source voltage rating of 30 V.

The SS14 also provides a 40 V reverse-voltage rating, giving more than a 3× margin over the 12 V supply voltage[2].

Although the SS14 is rated for 1 A average forward current, flyback operation is transient rather than continuous. The diode conducts only during MOSFET turn-off events while the inductive energy stored in the load is dissipated. For the intended low-frequency switching application, this rating is considered adequate.

Lower-voltage alternatives such as the SS12 would provide less voltage margin, while signal diodes such as the 1N4148 are not intended to handle inductive-load recirculation currents and would therefore be unsuitable for this application.

### 4.4 MOSFET Switching Stage

Q1 (AO3400A, `Package_TO_SOT_SMD:SOT-23`) is the primary switching device[1]. The drain is connected to the DRAIN net and the source is connected to GND.

The AO3400A was selected because:

- Gate threshold voltage \(V_{GS(th)} = 0.45\text{–}1.35\ \text{V}\) (at 250 µA), allowing reliable operation from both 3.3 V and 5 V logic sources.

- Low on-resistance. The datasheet specifies approximately 50 mΩ at \(V_{GS}=4.5\ \text{V}\); however, because this design is intended to operate from either 3.3 V or 5 V logic, the actual \(R_{DS(on)}\) at 3.3 V drive will be somewhat higher than the 4.5 V specification. To account for this uncertainty, the thermal analysis presented in Section 6.5 uses a more conservative assumed value of 80 mΩ when estimating conduction losses and junction-temperature rise.

- Continuous drain current rating of 5.7 A at 25 °C (package-limited), providing substantial margin above the nominal operating current range of 0.5–1.0 A. In practice, PCB trace current capability and thermal considerations limit the design current to 2.0 A, making the MOSFET itself non-limiting within the intended operating range.

- Drain-source voltage rating \(V_{DS}=30\ \text{V}\), exceeding the minimum design requirement of 14.4 V (\(1.2 \times V_{IN}\) for a 12 V supply) and providing additional margin for switching transients.

- Alternative MOSFETs were considered. Devices such as the 2N7002 and BSS138 are widely available in SOT-23 packages but provide significantly higher on-resistance and lower current capability. Higher-performance devices such as the IRLML6244 offer lower on-resistance but were not required for the intended 2 A design current. The AO3400A therefore represents a practical compromise between cost, availability, package size, and current-handling capability.

The schematic shows the SOURCE net label on Q1's source pin. As documented in Section 9.1, this creates an ERC warning because the GND power symbol and the SOURCE net label are intentionally placed on the same electrical node. The warning is acknowledged and discussed in detail in that section.

### 4.5 Gate Drive Network

The gate drive network consists of two resistors.

**R1 (47 Ω, `R_0603_1608Metric`)** is connected in series between the SIG net (from J3 Pin 1) and the GATE net (Q1 gate).

The AO3400A datasheet specifies an input capacitance of approximately 560 pF. The resulting RC time constant is:

```text
τ = RC
  = 47 Ω × 560 pF
  ≈ 26 ns
```

The corresponding 10–90% rise time is approximately:

```text
tr ≈ 2.2τ
   ≈ 57 ns
```

This switching speed is significantly faster than required for a simple DC load switch while still limiting peak gate charging current and reducing high-frequency switching noise. Smaller resistor values would provide little practical benefit, while substantially larger values would unnecessarily slow the switching transition.

For the intended application, the board operates as a low-frequency DC load switch rather than a high-speed switching converter. Consequently, precise optimisation of switching speed is unnecessary, and a conservative gate resistor value was preferred to reduce switching noise and ringing.

**R2 (100 kΩ, `R_0603_1608Metric`)** is connected between the GATE net and GND.

The pull-down resistor ensures that Q1 remains off whenever the logic input is disconnected or in a high-impedance state. The selected value is large enough to minimise static current consumption while remaining low enough to discharge the gate capacitance reliably.

When a 3.3 V logic signal drives the gate through R1, the voltage divider formed by R1 and R2 produces:

```text
VGS ≈ 3.3 V × 100000/(100000 + 47)
    ≈ 3.30 V
```

Therefore, R2 has a negligible effect on gate-drive voltage.

The static power dissipation in R2 when the gate is held high is:

```text
P = V²/R
  = (3.3 V)² / 100 kΩ
  ≈ 0.11 mW
```

which is negligible.
A substantially smaller value (for example 10 kΩ) would increase static current consumption without providing significant practical benefit, while a substantially larger value (for example 1 MΩ) would increase susceptibility to leakage currents and noise. The selected value of 100 kΩ therefore represents a common engineering compromise between reliable gate discharge and low standby power consumption.

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

### 5.1 Board Outline and Dimensions

The final PCB dimensions are **39.37 mm × 27.94 mm** (approximately 1.55 in × 1.10 in), as shown in Figure 5.

The board dimensions were selected by the designer rather than imposed by the project specification. The chosen size provides sufficient area for the required functional blocks while maintaining a compact layout. The board accommodates three connectors (J1, J2, and J3), the MOSFET switching stage, flyback protection diode, decoupling capacitors, LED indicator circuit, and test points without component crowding.

A larger PCB would have increased routing distances unnecessarily, resulting in longer current paths and a less compact design. Conversely, a smaller PCB would have reduced routing flexibility, increased component density, and made it more difficult to maintain appropriate spacing between the power path, gate-drive network, and test points.

The selected dimensions therefore represent a practical compromise between compactness, manufacturability, routing simplicity, and accessibility for assembly and testing.

Figure 5 shows the completed PCB layout within the final board outline.
> **Figure 5**
> <img width="1034" height="697" alt="スクリーンショット 2026-05-31 015135" src="https://github.com/user-attachments/assets/ec5f8393-b40a-4153-aba1-9bcd54cf0ac1" />


### 5.2 Component Placement Strategy

Component placement was performed with the objective of minimising current-path length, maintaining a logical signal flow, and simplifying assembly and testing. The resulting placement can be observed in Figures 5 and 6.

The power input connector **J1** is located on the left side of the PCB, establishing a clear entry point for the 12 V supply. Positioning J1 at the board edge simplifies cable access and provides a convenient location for the decoupling network immediately downstream of the supply input.

The decoupling capacitors **C1** and **C2** are placed directly adjacent to J1. This minimises the impedance between the supply connector and the capacitors, allowing transient current demands to be supplied locally rather than through the external power wiring. Keeping the decoupling capacitors close to the supply entry point reduces supply-voltage disturbance during load switching.

The switching MOSFET **Q1** is positioned near the centre of the PCB. This location provides a balanced compromise between several competing requirements. It keeps the high-current DRAIN connection reasonably short, allows a direct connection to the GND network through the SOURCE terminal, and places the MOSFET close to the gate-drive components. Moving Q1 significantly closer to the load connector would reduce DRAIN-trace length but would increase gate-drive routing length and reduce placement flexibility for the surrounding circuitry.

The gate-drive components **R1** and **R2** are placed immediately adjacent to the MOSFET gate connection. Short gate traces reduce parasitic inductance and minimise the possibility of switching transients causing unwanted gate ringing. This arrangement also simplifies routing of the SIG and GATE nets.

The logic connector **J3** is positioned in the lower portion of the PCB, physically separated from the high-current VIN and DRAIN paths. This separation reduces the likelihood of noise coupling between the power-switching circuitry and the external logic interface.

The load connector **J2** is positioned on the right side of the PCB to create a clear left-to-right current-flow path from power input to switched load output. The flyback diode **D1** is located adjacent to J2 so that inductive current can circulate through the shortest possible path when the MOSFET switches off.

The LED indicator components **R3** and **D2** are placed near the DRAIN node that they monitor. This minimises routing complexity and maintains a direct electrical relationship between the indicator circuit and the switching node.

Finally, test points **TP1 (DRAIN)** and **TP2 (GND)** are placed in accessible locations to allow oscilloscope and multimeter measurements without requiring contact with component leads or connector pins. This improves troubleshooting and functional verification during testing.

The final placement produces a logical physical arrangement that follows circuit functionality while supporting manufacturability, inspection, and measurement access.

### 5.3 Routing Strategy

The PCB was routed using a combination of F.Cu and B.Cu layers, with routing decisions based on current requirements, noise reduction, and manufacturability.

The highest-current paths are the VIN and DRAIN nets. These traces carry the load current and were therefore assigned a width of 2.0 mm. According to IPC-2152 guidance for 1 oz copper, this width provides adequate current-carrying capability for the 2.0 A design current while maintaining a modest temperature rise. Because the MOSFET itself is capable of substantially more current than the PCB traces are designed to carry, the PCB routing rather than the semiconductor device becomes the practical design limit.

The VIN trace was routed directly from J1 to J2 using the shortest practical path. This minimises conductor resistance and voltage drop between the supply connector and the load connector. The DRAIN trace was similarly routed using a short and direct connection between J2 and Q1 to minimise power loss and reduce the area enclosed by the high-current switching loop.

The GATE and SIG nets carry only small logic-level currents and therefore use a width of 0.25 mm. Wider traces were unnecessary and would have occupied additional routing area without providing any electrical benefit. The gate-drive path was intentionally kept short, with R1 placed immediately adjacent to the gate pin of Q1. Minimising gate-trace length reduces parasitic inductance and lowers the likelihood of switching transients or gate ringing.

Several vias were used to connect traces and copper regions between F.Cu and B.Cu. Via placement was kept to the minimum required for routing flexibility and copper-zone connectivity. Wherever possible, high-current traces were routed without unnecessary layer transitions, since each additional via introduces a small amount of resistance and inductance. Vias associated with the GND network also assist in connecting the F.Cu and B.Cu ground zones, improving overall ground continuity.

Figure 4 shows the intermediate routing stage before the F.Cu ground zone was added. At this stage, only routed tracks and the B.Cu ground zone were present. Figure 5 shows the completed routing after the addition of the F.Cu ground zone and final copper refill. Comparing Figures 4 and 5 demonstrates how the additional F.Cu copper zone completed the GND connectivity for the surface-mount components.

No acute-angle traces were used, and all routing follows standard PCB design practice intended to simplify manufacturing and inspection.

### 5.4 Ground Plane Implementation

GND copper zones were implemented on both F.Cu and B.Cu layers and assigned to the GND net.

During PCB development, an intermediate layout revision contained only a B.Cu GND zone. As shown in Figure 4, the top copper layer consisted only of routed traces and did not include an F.Cu copper fill. Although the GND net was correctly defined in the schematic, several GND-connected SMD pads located on F.Cu were not physically connected to the ground network. This condition was later identified through DRC and is discussed in detail in Section 10 (Problem 2).

The final implementation is shown in Figure 5. Compared with Figure 4, the F.Cu GND zone has been added and refilled, providing direct copper connectivity between all GND-connected SMD pads on the component side. The B.Cu zone continues to provide the primary return plane, while the F.Cu zone improves local connectivity and reduces the need for additional routing.

Using GND copper on both layers simplifies routing, improves return-current paths, increases available copper area for heat spreading, and ensures reliable electrical connectivity for all GND nodes.

### 5.5 Silkscreen

Reference designators and functional labels were included on the F.SilkS layer to improve assembly and troubleshooting.

As confirmed by the 3D Viewer (Figure 6), all component reference designators remain readable and clear of pads, solder-mask openings, and courtyard boundaries. During PCB development, several reference designators were repositioned to resolve silkscreen-clearance warnings identified by DRC.

The final silkscreen placement allows all major components, connectors, and test points to be identified directly from the assembled PCB without requiring reference to the schematic.

### 5.6 3D Viewer Inspection

Figure 6 shows the completed PCB in the KiCad 3D Viewer from the top side.

The inspection was used to verify component orientation, footprint placement, and overall mechanical consistency before manufacturing-file generation. The AO3400A MOSFET (Q1), SS14 diode (D1), capacitors (C1 and C2), resistors (R1–R3), LED indicator (D2), and connectors (J1–J3) are all visible and positioned consistently with the schematic.

Particular attention was paid to:

- MOSFET orientation and pin assignment
- Flyback diode polarity
- Pin-1 orientation of connectors
- Test-point accessibility
- Silkscreen readability
- Component-to-component clearance

The locations of TP1 (DRAIN) and TP2 (GND) can also be confirmed in Figure 6, demonstrating convenient access for oscilloscope probing and troubleshooting.

Figure 7 shows the underside of the PCB. The through-hole connector pins and test-point leads are visible extending through the board and available for soldering. Figure 7 also confirms that no SMD components are mounted on the bottom layer, simplifying assembly because all surface-mount components are located on a single side of the PCB.
> **Figure 6**
> <img width="1020" height="751" alt="スクリーンショット 2026-05-31 062012" src="https://github.com/user-attachments/assets/99108505-532c-4583-a931-2396b213c7aa" />
> **Figure 7**
> <img width="852" height="641" alt="スクリーンショット 2026-05-31 062022" src="https://github.com/user-attachments/assets/781db0c6-1dea-4f74-8a97-71dee9621af5" />



---

## 6. EMC, Grounding, and Thermal Considerations
### 6.1 Decoupling Capacitor Placement

C1 (100 nF, `C_0603_1608Metric`) and C2 (10 µF, `C_0805_2012Metric`) are placed immediately adjacent to the power-input connector J1, as shown in Figures 5 and 6. Positioning the decoupling network near the supply entry point minimises the impedance between the external power source and the local PCB power-distribution network.

The capacitor values were selected to address different frequency ranges of load-switching disturbance:

- **C1 (100 nF ceramic)** provides low impedance at high frequencies and suppresses fast switching transients.
- **C2 (10 µF ceramic)** provides bulk energy storage and supports lower-frequency load-current variations.

The combination of a 100 nF capacitor and a larger bulk capacitor is a widely used decoupling strategy because no single capacitor maintains low impedance across the entire frequency spectrum. Together, the capacitors help maintain a stable VIN rail during load-switching events and reduce sensitivity to supply-cable inductance.

Both capacitors are rated at 16 V, providing a voltage margin of approximately 33% above the maximum 12 V supply voltage. This satisfies common engineering practice by providing additional reliability margin and reducing the effects of capacitance reduction caused by DC-bias voltage.

Where ceramic capacitors are used, X7R or X5R dielectric types are generally preferred because they provide significantly better capacitance stability over voltage and temperature than lower-cost alternatives such as Y5V or Z5U. These dielectric types are therefore appropriate for both the high-frequency decoupling and bulk-storage functions implemented in this design.

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

The final PCB uses GND copper zones on both F.Cu and B.Cu layers, as shown in Figure 5.

The dual-layer implementation provides low-impedance return-current paths, improves connectivity between GND nodes, and increases available copper area for thermal spreading. The detailed design iteration that led to this implementation is discussed in Section 10 (Problem 2).

By maintaining continuous GND copper on both layers, the design reduces routing complexity and improves overall electrical robustness[7].

### 6.4 Gate Drive and EMI Considerations

The MOSFET gate is driven through R1 (47 Ω), connected in series between the SIG input and the AO3400A gate.

The AO3400A datasheet specifies a typical input capacitance of approximately:

```text
Ciss ≈ 560 pF
```

The resulting gate-drive RC time constant is:

```text
τ = R × C
  = 47 Ω × 560 pF
  ≈ 26 ns
```

The corresponding approximate 10–90% rise time is:

```text
tr ≈ 2.2τ
   ≈ 57 ns
```

This switching speed is more than adequate for the intended DC on/off switching application while still limiting peak gate-charging current and reducing high-frequency spectral content.

R2 (100 kΩ) provides a pull-down path from the gate to GND. When a 3.3 V logic signal is applied, the voltage-divider effect between R1 and R2 is negligible:

```text
VGS ≈ 3.3 V ×
      (100000 / (100000 + 47))
    ≈ 3.30 V
```

Thus, R2 does not materially reduce the gate-drive voltage during normal operation.

The power dissipated in R2 when the gate is held high is:

```text
P = V²/R
  = (3.3 V)² / 100000 Ω
  ≈ 0.11 mW
```

which is negligible.

As shown in Figures 5 and 6, R1 is positioned immediately adjacent to the MOSFET gate pin. Keeping the gate-routing length short reduces parasitic inductance and minimises susceptibility to noise pickup, helping to prevent unwanted gate ringing during switching transitions.

### 6.5 Thermal Analysis

The dominant power loss in Q1 is MOSFET conduction loss, which can be estimated using:

```text
P = I² × RDS(on)
```

The AO3400A datasheet specifies an on-resistance of approximately 50 mΩ at a gate-drive voltage of \(V_{GS}=4.5\ \text{V}\). Because this design is intended to operate from either 3.3 V or 5 V logic sources, the actual on-resistance depends on the applied gate voltage. At 3.3 V gate drive, the effective \(R_{DS(on)}\) is expected to be somewhat higher than the 4.5 V specification.

To provide a conservative engineering estimate, a value of 80 mΩ was assumed for thermal calculations. This value lies between the datasheet on-resistance specified at \(V_{GS}=2.5\ \text{V}\) and \(V_{GS}=4.5\ \text{V}\), and therefore provides a reasonable approximation for operation from a 3.3 V logic source.

**Table 5: Estimated MOSFET Conduction Loss**

| Load Current | Assumed R_DS(on) | Power Dissipation |
|-------------|------------------|------------------|
| 1.0 A | 80 mΩ | 0.08 W |
| 2.0 A | 80 mΩ | 0.32 W |

The AO3400A datasheet specifies a junction-to-ambient thermal resistance of approximately:

```text
θJA ≈ 100 °C/W
```

for the recommended PCB mounting condition.

Using the worst-case design current of 2.0 A:

```text
ΔTJ = P × θJA
     = 0.32 W × 100 °C/W
     ≈ 32 °C
```

Assuming an ambient temperature of 25 °C:

```text
TJ ≈ 25 °C + 32 °C
   ≈ 57 °C
```

This estimated junction temperature remains well below the AO3400A maximum rated junction temperature of 150 °C.

The calculation above is intentionally conservative because it assumes a higher on-resistance than the 4.5 V datasheet value while simultaneously using the full 2.0 A design current. In normal operation, where the expected load current is approximately 0.5–1.0 A, the actual power dissipation and junction temperature rise will be substantially lower.

As shown in Figure 5, Q1 is connected to relatively large copper areas through both the source and drain terminals. These copper regions provide additional heat spreading beyond the minimum datasheet test condition and are expected to reduce the effective thermal resistance further.

Consequently, thermal performance is not expected to be the limiting factor for the intended operating range. Instead, PCB trace current capacity and connector ratings become the primary constraints of the design.

**Table 6: Trace Width Design Summary**

| Net | Design Current | Selected Width | Rationale |
|------|------|------|------|
| VIN | 2.0 A | 2.0 mm | IPC-2152 guidance for 1 oz copper |
| DRAIN | 2.0 A | 2.0 mm | Main load-current path |
| GATE | < 5 mA | 0.25 mm | Logic signal |
| SIG | < 5 mA | 0.25 mm | Logic input |
| LED path | ~3 mA | 0.25 mm | Indicator current only |

---
 
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

The generated Gerber and drill files were inspected using a Gerber viewer prior to submission to verify that the manufacturing data matched the intended PCB design.

The inspection confirmed that the board outline was correctly defined as a single closed contour on the Edge.Cuts layer, with no gaps or overlapping segments that could cause fabrication errors.

The F.Cu and B.Cu layers were checked to ensure that all copper features appeared in the expected locations. Power traces, signal traces, copper zones, and component pads matched the PCB layout shown in Figure 5.

The solder-mask layers were reviewed to confirm that all pads contained appropriate mask openings and that no unintended mask clearances were present on copper features.

The silkscreen layers were inspected to verify that reference designators and markings remained readable and did not overlap exposed copper pads or drill locations.

Finally, the Excellon drill data were examined to confirm that all plated through-holes, connector pins, test points, and vias were centred correctly within their corresponding pad geometries.

No discrepancies were identified during the Gerber-viewer inspection, indicating that the manufacturing files accurately represent the final PCB layout.

### 7.6 Assembly Considerations

All SMD components are on F.Cu only, enabling single-sided SMD assembly. Through-hole connectors (J1, J2, J3) and test points (TP1, TP2) are hand-solderable from the bottom side, as confirmed by the 3D bottom view in Figure 7. D2 (LED, 0805) has polarity indicated by the silkscreen. J1 Pin 1 (VIN) and J2 Pin 1 (LOAD+) are labelled on the silkscreen, as visible in Figure 6.

---

## 8. Bill of Materials and Sourcing

Table 7 provides the complete Bill of Materials using the exact footprints as placed in the KiCad PCB file.

**Table 7: Bill of Materials**

| Ref | Description | Value | Footprint | MPN (Example) | Qty | Key Rating |
|-----|-------------|-------|-----------|---------------|-----|------------|
| Q1 | N-Ch MOSFET | AO3400A | `Package_TO_SOT_SMD:SOT-23` | AO3400A | 1 | V_DS = 30 V; I_D = 5.7 A; R_DS(on) ≈ 50 mΩ at V_GS = 4.5 V (higher at 3.3 V drive) |
| R1 | Gate series resistor | 47 Ω | `R_0603_1608Metric` | Any 47 Ω 1% 0603 | 1 | Limits gate charge current; 1/10 W rated |
| R2 | Gate pull-down | 100 kΩ | `R_0603_1608Metric` | Any 100 kΩ 1% 0603 | 1 | Holds gate low when J3 is disconnected |
| R3 | LED current limiter | 3.3 kΩ | `R_0603_1608Metric` | Any 3.3 kΩ 1% 0603 | 1 | I_LED ≈ 3 mA; P_R3 = 30 mW < 100 mW rating |
| C1 | Decoupling capacitor | 100 nF | `C_0603_1608Metric` | Any 100 nF 16 V X7R 0603 | 1 | 16 V rating > 12 V; X7R for stability |
| C2 | Bulk decoupling | 10 µF | `C_0805_2012Metric` | Any 10 µF 16 V X5R 0805 | 1 | 16 V rating > 12 V; bulk energy storage |
| D1 | Flyback diode | SS14 | `Diode_SMD:D_SMA` | SS14 (Vishay or equiv.) | 1 | Schottky; 1 A fwd; 40 V reverse |
| D2 | LED indicator | LED | `LED_SMD:LED_0805_2012Metric` | Any standard LED 0805 | 1 | Indicator only; ≈ 3 mA operating |
| J1 | Power input | 1×02 | `PinHeader_1x02_P2.54mm_Vertical` | Generic 2.54 mm header | 1 | Suitable for prototype power connections |
| J2 | Load output | 1×02 | `PinHeader_1x02_P2.54mm_Vertical` | Generic 2.54 mm header | 1 | Suitable for prototype load connections |
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

The ERC result is shown in Figure 2. The ERC window reports **0 errors** and **2 warnings**, confirming that no electrical-rule violations remain in the final schematic. The two warnings listed in the ERC results are both related to multiple labels being applied intentionally to the same electrical node.

> **Figure 2**
> <img width="1237" height="609" alt="スクリーンショット 2026-05-30 122352" src="https://github.com/user-attachments/assets/ca5ce4cb-b1c2-4140-8508-9ffbbd8d4736" />


As visible in Figure 2, the ERC summary shows a zero-error count together with two warning entries. These warnings arise because KiCad detects that different labels have been attached to the same electrical node and therefore informs the designer which label will be used as the canonical net name in the generated netlist.[4]

The two warnings are:

1. **`Both DRAIN and LOAD- are attached to the same items; DRAIN will be used in netlists`**

   The node connecting Q1 drain and J2 Pin 2 carries both the `DRAIN` net label and the `LOAD−` functional label. The two labels intentionally describe the same electrical node from different perspectives: `DRAIN` identifies the MOSFET terminal, while `LOAD−` identifies the load connector terminal. Because both labels refer to the same copper connection, KiCad reports a warning and selects `DRAIN` as the canonical net name for netlist generation.

2. **`Both GND and SOURCE are attached to the same items; GND will be used in netlists`**

   The node at the source terminal of Q1 carries both the GND power symbol and the `SOURCE` net label. The `SOURCE` label was included to improve schematic readability and clearly identify the MOSFET terminal. Since both labels intentionally refer to the same electrical node, KiCad reports a warning and uses `GND` as the canonical net name.

These warnings are acknowledged and accepted because they do not indicate incorrect connectivity. In both cases, the duplicated labels were intentionally added to improve schematic readability while referring to electrically identical nodes. Consequently, the ERC completed successfully with **0 errors** and only the two expected warnings shown in Figure 2.

---

## 10. Problems Encountered and Solutions

### 10.1 Design Iteration and Debugging Process

The design was developed iteratively, with ERC and DRC run at each significant milestone[5]. The key stages were:

1. **Schematic capture and ERC** — The schematic was drawn and the ERC was run to verify net connectivity. An ERC error was raised because J3's GND pin lacked a connected `PWR_FLAG` symbol (Problem 1 below). This was resolved before layout began.

2. **Component placement** — All 13 components were imported into the PCB editor and placed according to the intended left-to-right power-flow strategy.

3. **Trace routing** — VIN and DRAIN power traces were routed at 2.0 mm; gate, signal, and indicator traces at 0.25 mm. GND copper zones were initially applied to B.Cu only.

4. **First DRC run — GND connectivity errors identified** — Running DRC with zone refill enabled revealed multiple missing-connection errors on F.Cu GND pads (Problem 2 below). A second GND zone was added to F.Cu to resolve these.

5. **Final DRC and ERC** — All checks confirmed: 0 DRC errors, 0 DRC warnings, 0 ERC errors (2 acknowledged net-label warnings).

6. **3D Viewer inspection** — Final visual check of component orientations, silkscreen readability, and board shape (Figures 6 and 7).

---

### Problem 1: ERC Error — Missing PWR_FLAG on the GND Net

#### Symptom

During an early ERC run, KiCad reported an error indicating that an input-power pin was not driven by any recognised power-source output.

Although the GND symbols were electrically connected throughout the schematic, the ERC engine could not identify a valid power source associated with the GND network. As a result, ERC reported the message:

```text
Input Power pin not driven by any Output Power pins
```

and the design could not achieve a clean ERC result.

Figure 8 shows the ERC window at the time the error was detected. The ERC report contains one error and identifies a power-input pin that KiCad considered undriven.

> **Figure 8**
> <img width="822" height="604" alt="スクリーンショット 2026-05-30 052905" src="https://github.com/user-attachments/assets/9521eed7-369a-4460-826c-900eac46c1f1" />


#### Root Cause

KiCad's ERC system distinguishes between ordinary electrical connections and nets that are recognised as valid power sources.

In the initial version of the schematic, a PWR_FLAG symbol had been placed only on the VIN net near connector J1. No PWR_FLAG was connected to the GND net.

Although the GND symbols visually connected all ground nodes together, KiCad treats power symbols such as GND as power-input pins rather than power sources. Because the ERC engine could not identify any component explicitly driving the GND net, it treated the associated power-input pins as undriven and generated an ERC violation.

The reported message was:

Input Power pin not driven by any Output Power pins

This behaviour is common in KiCad designs where power is supplied through external connectors rather than through components that KiCad recognises as power-output devices. In such cases, a PWR_FLAG symbol is required to inform the ERC engine that the net is intentionally powered by an external source.

#### Solution

A PWR_FLAG symbol was added to the GND net to indicate that the ground network is supplied by an external source and should be treated as a valid power net by the ERC engine.

After adding the PWR_FLAG and re-running ERC, the "Input Power pin not driven by any Output Power pins" error shown in Figure 8 disappeared and the schematic passed ERC without power-connectivity violations.

The final schematic shown in Figure 1 contains:

- one `PWR_FLAG` on the VIN net,
- one `PWR_FLAG` on the GND net.

The final ERC result is shown in Figure 2. At that stage of development, the original power-pin error had been resolved completely. The two warnings visible in Figure 2 were introduced later by the intentional addition of duplicate net labels (`DRAIN` / `LOAD−` and `SOURCE` / `GND`) and are discussed separately in Section 9.1.

#### Key Lesson

When a design receives power from external connectors rather than from explicit power-source components, both the positive supply rail and the return rail should be identified to the ERC engine.

In KiCad, this is commonly achieved by placing `PWR_FLAG` symbols on the externally supplied power nets. A PWR_FLAG on the positive supply rail alone may not be sufficient. Any externally supplied power net that is interpreted as a power-input network by ERC, including the return (GND) rail, must be recognised by the ERC engine to avoid false power-connectivity errors.

---

### Problem 2: Missing GND Connections on F.Cu (DRC Errors)

#### Symptom

During PCB verification, the Design Rule Check (DRC) reported multiple **"Missing connection between items"** errors involving pads assigned to the GND net.

As shown in Figure 3, the DRC window reported six connectivity violations associated with GND-net pads. These errors indicated that pads belonging to the same net were not physically connected by copper despite being logically connected in the schematic.

> **Figure 3**
> <img width="809" height="721" alt="スクリーンショット 2026-05-31 015000" src="https://github.com/user-attachments/assets/08e6cf50-8eca-4b30-b5f6-1cd8ba83e615" />

Examples reported by DRC included:

- C1 Pad 2 (GND) ↔ J1 Pad 2 (GND)
- C2 Pad 2 (GND) ↔ C1 Pad 2 (GND)
- TP2 Pad 1 (GND) ↔ R2 Pad 2 (GND)
- R2 Pad 2 (GND) ↔ Q1 Source Pad (GND)

Figure 4 shows the PCB layout at the stage when these errors occurred.

> **Figure 4**
> <img width="1039" height="710" alt="スクリーンショット 2026-05-31 012622" src="https://github.com/user-attachments/assets/5d604998-b782-4ddc-a777-2b02d3f5e552" />

Observe that Figure 4 contains routed copper traces but does not yet contain a continuous GND copper zone on the F.Cu layer. Although the affected pads were correctly assigned to the GND net in the schematic and netlist, no valid physical copper connection existed between several of the associated top-layer pads.

#### Root Cause

At this stage of development, a GND copper zone had been implemented only on the B.Cu layer. However, several GND-connected SMD pads—including those belonging to C1, C2, R2, Q1, and TP2—were located on F.Cu.

Because no dedicated GND traces, stitching vias, or F.Cu GND copper zone connected these pads, the pads belonged to the same logical net but remained physically isolated in the PCB layout. KiCad therefore correctly reported the missing physical connections during DRC.

This issue highlights an important distinction between schematic connectivity and PCB connectivity. A net can appear fully connected in the schematic while still remaining electrically open in the physical PCB implementation if copper connections are not provided.

Had the board been manufactured in this state, several GND-connected components would have been electrically disconnected from the ground network, preventing correct circuit operation.

#### Solution

To resolve the issue, an additional GND copper zone was created on the F.Cu layer and assigned to the GND net. After executing **Fill All Zones**, the new copper zone established physical connections between all affected GND pads on the component side of the board.

The corrected implementation is shown in Figure 5.

> **Figure 5**
> *(Final PCB layout with GND zones on both F.Cu and B.Cu)*

Compared with Figure 4, Figure 5 includes a continuous F.Cu GND copper fill surrounding and connecting the SMD GND pads. This additional copper region provides complete electrical connectivity for the top-layer GND network.

Following the zone refill, all reported missing-connection errors were eliminated and the PCB passed DRC without connectivity violations.

#### Key Lesson

Copper zones form part of the electrical connectivity of the PCB and must therefore be verified using DRC rather than visual inspection alone.

When SMD components are placed on F.Cu, a B.Cu ground plane alone does not automatically provide electrical connectivity to top-layer GND pads. Physical connections must be created using traces, vias, or an F.Cu copper zone.

In this project, the issue was identified because DRC was executed with **"Refill all zones before performing DRC"** enabled, as shown in Figure 3. This setting ensures that zone connectivity is evaluated using the latest copper geometry and helps reveal connectivity errors that might otherwise remain hidden during layout review.

The experience reinforced the importance of treating copper zones as active electrical conductors rather than purely mechanical layout features.

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

The AO3400A MOSFET was selected in a SOT-23 package to maintain a compact PCB footprint and minimise board area while remaining compatible with the intended current range. For the nominal operating current of 0.5–1.0 A and the 2.0 A design-current target, the estimated conduction losses remain relatively low, making a larger power package unnecessary for the intended application. As discussed in Section 6.5, the actual MOSFET on-resistance depends on gate-drive voltage. The commonly cited value of approximately 50 mΩ corresponds to \(V_{GS}=4.5\ \text{V}\); operation from a 3.3 V logic source will result in a somewhat higher \(R_{DS(on)}\), increasing conduction loss relative to the simplified estimate.

However, thermal performance in small packages depends strongly on PCB copper area, ambient temperature, airflow, and operating conditions. For applications requiring sustained operation near the 2 A design-current limit, or operation at elevated ambient temperatures, a larger package such as SOT-223 or DPAK would provide additional thermal margin and improved heat dissipation capability.

The design uses standard 2.54 mm pin-header connectors for J1 and J2. These connectors are inexpensive, widely available, and suitable for laboratory use and prototype evaluation. Nevertheless, screw-terminal connectors would provide improved mechanical robustness and may be preferable for applications involving frequent cable installation or higher continuous currents.

Another limitation is that the project focused on schematic development, PCB layout, design verification, and manufacturing-file generation. While ERC, DRC, 3D inspection, and manufacturing-output checks were completed successfully, no fabricated prototype was available during the project. Consequently, electrical performance under real operating conditions remains to be verified experimentally.

### 11.4 Potential Future Improvements

- **Screw-terminal connectors for J1 and J2:** Improve mechanical robustness and current-handling capability for power connections.

- **Additional GATE test point:** A dedicated test point on the GATE net would simplify oscilloscope measurements of the MOSFET gate waveform.

- **Larger MOSFET package:** Replacing the SOT-23 package with SOT-223 or DPAK would provide additional thermal margin for sustained high-current operation.

- **Transient-voltage-suppression (TVS) protection:** A TVS diode across the VIN input could improve resilience against supply-line voltage spikes.
- **Dedicated 5 V gate-drive stage:** Although the AO3400A operates from both 3.3 V and 5 V logic sources, a dedicated gate driver or 5 V gate-drive stage would further reduce \(R_{DS(on)}\), minimise conduction losses, and improve switching performance at higher load currents.

## 12. Conclusion

The Low-Side MOSFET Load Driver PCB was designed, iteratively debugged, verified, and prepared for manufacturing within the constraints of the project specification. The board measures 39.37 mm × 27.94 mm on a 2-layer FR4 substrate (1.6 mm, 1 oz copper) and implements a complete logic-controlled DC load switch using an AO3400A N-channel MOSFET. Load switching is performed exclusively by a digital logic signal applied to connector J3; no mechanical switching element is present on the board.

All design objectives were met:

- Low-side switch topology correctly implemented for 12 V loads up to 1 A nominal (2 A design margin).
- Power traces sized to 2.0 mm for the 2 A design current in accordance with IPC-2152 guidance.
- Gate series resistor (R1 = 47 Ω) and gate pull-down resistor (R2 = 100 kΩ) correctly implemented.
- SS14 flyback diode protects against inductive-load transients.
- Dual GND copper zones on F.Cu and B.Cu provide complete GND connectivity on both layers.
- Two test points provided: TP1 on the DRAIN net and TP2 on GND.
- Final verification results: **0 ERC errors**, **0 DRC errors**, and **0 DRC warnings**.
- Complete manufacturing package generated, including Gerber files, Excellon drill files, BOM, schematic documentation, and 3D Viewer verification images.

The two most important technical lessons from this project address different stages of the design process. At the schematic level, KiCad's ERC distinguishes between power-source and power-input symbols. When power is supplied through external connectors, the corresponding supply nets must be identified to the ERC engine using `PWR_FLAG` symbols; otherwise, ERC may report power-input pins as undriven even when the schematic is electrically correct. At the PCB-layout level, a GND copper zone must be present on every copper layer that contains SMD GND pads unless alternative copper connections such as traces or vias are provided. In this project, relying only on a B.Cu GND zone left several F.Cu GND pads physically disconnected. The issue was not obvious from visual inspection but was reliably detected by DRC with automatic zone refill enabled.

The completed design satisfies the functional, electrical, and manufacturability requirements defined for the project. The board can be fabricated using standard two-layer PCB manufacturing processes and the accompanying documentation provides sufficient information for independent review, assembly, and testing. Although no physical prototype was available during the project, the successful completion of ERC, DRC, 3D-model inspection, Gerber verification, and manufacturing-file generation provides confidence that the design is ready for fabrication and subsequent experimental validation.

---


## 13. References

[1] Alpha & Omega Semiconductor,
AO3400A N-Channel Enhancement Mode Field Effect Transistor Datasheet.
Available:
https://www.aosmd.com/products/mosfets/low-voltage-mosfets-12v-30v/ao3400a

[2] Vishay Semiconductors,
SS14 Surface-Mount Schottky Barrier Rectifier Datasheet.
Available:
https://www.vishay.com/search/?searchChoice=part&query=SS14

[3] KiCad Documentation Team,
KiCad 8 Documentation.
Available:
https://docs.kicad.org/

[4] KiCad Documentation Team,
Electrical Rules Checker (ERC).
Available:
https://docs.kicad.org/

[5] KiCad Documentation Team,
Design Rules Checker (DRC).
Available:
https://docs.kicad.org/

[6] Microchip Technology,
AN947: Power MOSFET Basics and Gate Drive Considerations.

[7] IPC,
IPC-2221: Generic Standard on Printed Board Design.

[8] Würth Elektronik,
PCB Layout Recommendations for Power Electronics.
Available:
https://www.we-online.com/

[9] SRH University of Applied Sciences,
PCB Design and Layout – Final Exam Brief,
Electronic Development Module,
Prof. Dr. Oezdemir Cetin.

---

*End of Report*
