# Day 2: Velocity Saturation and basics of CMOS Inverter VTC

This comprehensive guide covers basic of CMOS Inverter VTC, SPICE simulation for lower nodes and Velocity Saturation effect.

**Note:** Click the sections below to expand.

---

## Table of Contents

<details>
<summary>Introduction to CMOS Inverter VTC and Velocity Saturation</summary>

# CMOS Inverter and Advanced MOSFET Physics

This document condenses the attached content into topicwise, concise notes for easy understanding. Save as `CMOS_Inverter_MOSFET_Physics.md`.

***

## 1. CMOS Inverter Operation & Logic Behavior

A CMOS inverter uses complementary NMOS and PMOS transistors:

- Structure:
  - PMOS source to VDD, drain to output
  - NMOS source to GND, drain to output
  - Gates tied together as input, drains share output

- Logic States:
  - Vin = VDD: PMOS off, NMOS on → Vout = 0
  - Vin = 0: PMOS on, NMOS off → Vout = VDD

- Key Properties:
  - Rail-to-rail swing, zero static power, complementary operation

<img width="1916" height="1034" alt="Screenshot 2025-10-18 143626" src="https://github.com/user-attachments/assets/4292f4f0-c3f5-4efd-8ff0-4314d0064f98" />

***

## 2. Velocity Saturation in Scaled MOSFETs

Carrier velocity no longer scales linearly with electric field at high fields:

- Classical Drift: $$v_n = \mu_n E$$ for $$E < E_{crit}$$ (~1.5 MV/m)
- Saturation: Above $$E_{crit}$$, carriers reach $$v_{sat}\approx10^5\ \text{m/s}$$
- Short-Channel Impact:
  - Onset of velocity saturation before pinch-off
  - Reduced drive current, degraded $$g_m$$, non-ideal $$I\!-\!V$$

<img width="1705" height="695" alt="Screenshot 2025-10-18 143917" src="https://github.com/user-attachments/assets/ea405afb-92c9-42fc-93c4-753a98fd55d8" />

<img width="1854" height="988" alt="Screenshot 2025-10-18 165340" src="https://github.com/user-attachments/assets/02a5c138-7c84-4be3-ad42-6996604b5b21" />

***

## 3. CMOS Inverter Voltage Transfer Characteristic (VTC)

Defines regions as Vin sweeps from 0 to VDD:

| Region            | Vin Range           | NMOS         | PMOS         | Vout Behavior      |
|-------------------|---------------------|--------------|--------------|--------------------|
| Cutoff            | 0 to Vt,N           | OFF          | Linear ON    | Vout ≈ VDD         |
| Transition Low    | Vt,N to VDD/2       | Linear → Sat | Linear       | Vout falls         |
| Transition Mid    | Around VDD/2        | Sat          | Sat          | Sharp gain (Vth)   |
| Transition High   | VDD/2 to VDD−|Vt,P| Sat          | Linear       | Vout near GND      |
| Saturation Output | VDD−|Vt,P| to VDD   | Linear ON    | OFF (PMOS)   |                    |

- Switch Point: Occurs when both devices conduct, defining inverter threshold $$V_{th,inv}$$.

<img width="1917" height="957" alt="Screenshot 2025-10-18 175006" src="https://github.com/user-attachments/assets/94c5d331-7bfa-4c51-b15f-b8cc34cff614" />

***

## 4. MOSFET as a Voltage-Controlled Switch

Idealized two-state model:

- OFF State ($$V_{GS}<V_t$$): High $$R_{DS}$$, no conduction
- ON State ($$V_{GS}>V_t$$): Finite $$R_{ON}\propto1/(V_{GS}-V_t)$$
- Applications: Transmission gates, multiplexers, dynamic logic, SRAM
- Limitations: Finite $$R_{ON}$$, capacitances, body effect, leakage, velocity saturation

<img width="1919" height="1075" alt="Screenshot 2025-10-18 174924" src="https://github.com/user-attachments/assets/06d8a8b2-71f7-46cd-816d-24cd8b665269" />

***

## 5. Advanced Current Models with Velocity Saturation

Unified drain current expression accounting for saturation:

$$
I_D = k_n \Bigl[V_{gt}V_{min} - \tfrac{V_{min}^2}{2}\Bigr]\bigl(1 + V_{DS}\bigr)
$$

- $$V_{min} = \min(V_{gt}, V_{DS}, V_{DS,sat})$$
- Three Regimes:
  1. Linear: $$V_{DS}<V_{gt}$$
  2. Classical Sat.: $$V_{gt}<V_{DS}<V_{DS,sat}$$
  3. Velocity Sat.: $$V_{DS,sat}<V_{DS}$$

- Key: $$V_{DS,sat}$$ set by critical field ($$\sim0.15$$ V for 100 nm).


<img width="1854" height="988" alt="Screenshot 2025-10-18 165340" src="https://github.com/user-attachments/assets/b9afb977-3899-4f57-b2f2-7f6b878b5a29" />

***

## 6. SPICE Simulation Results: Geometry Effects

Comparison of two NMOS devices with identical $$W/L$$ but differing absolute sizes:

| Parameter        | Long Channel (1.8 µm/1.2 µm) | Short Channel (0.375 µm/0.25 µm) |
|------------------|------------------------------|----------------------------------|
| $$W/L$$ Ratio    | 1.5                          | 1.5                              |
| Peak $$I_D$$     | 410 µA                       | 210 µA                           |
| Saturation Mode  | Pinch-off limited            | Velocity saturated               |
| Transconductance | Higher                       | Degraded                         |

- Conclusion: Short-channel devices suffer ~50% current reduction due to velocity saturation.


<img width="1919" height="1034" alt="Screenshot 2025-10-18 174503" src="https://github.com/user-attachments/assets/94bfbb04-4ddc-4da8-a80a-edc95ed4bb20" />

***

## 7. CMOS Inverter Load-Line Analysis

Graphical method to find DC operating point:

- Load Line: PMOS $$I\!-\!V$$ curve transformed onto NMOS axes.
- Operating Point: Intersection of NMOS characteristic and PMOS load line for each Vin.
- Transitions: Moves along load line as Vin changes, shaping the VTC.

<img width="1896" height="1074" alt="Screenshot 2025-10-18 175432" src="https://github.com/user-attachments/assets/ae76d11b-fcd9-46ef-80a9-bb2603f85b48" />


***

---

</details>

<details>
<summary>Practical Labs Implementation</summary>

## Plotting Id vs Vds 

### Spice Simulation code

```
gedit day2_nfet_idvds_L015_W039.spice
```

```bash
*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description

XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=0.39 l=0.15
R1 n1 in 55

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op
.dc Vdd 0 1.8 0.1 Vin 0 1.8 0.2

.control

run
display
setplot dc1
.endc

.end
```

<img width="1919" height="754" alt="image" src="https://github.com/user-attachments/assets/5026c388-cc74-4203-8c06-e32cb58fb906" />

### Simulate the Circuit
```
ngspice day2_nfet_idvds_L015_W039.spice 
plot -vdd#branch
```

<img width="1919" height="903" alt="image" src="https://github.com/user-attachments/assets/293d4f1a-1081-481b-83ed-e54663ed4673" />

## Graph

<img width="1919" height="988" alt="image" src="https://github.com/user-attachments/assets/10442752-f201-4fb3-a206-043fd458bcc7" />

- The graph shows PMOS transistor output characteristics (I_D vs. V_DS for different V_GS).
- Higher negative V_GS values produce higher drain current for the same V_DS.
- The curve transitions from linear (rising) to saturation (flattened) regions.

## Plotting Id vs Vgs 

### Spice Simulation code

```
gedit day2_nfet_idvgs_L015_W039.spice
```

```bash
*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description

XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=0.39 l=0.15

R1 n1 in 55

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op
.dc Vin 0 1.8 0.1 

.control

run
display
setplot dc1
.endc

.end
```

<img width="1919" height="686" alt="image" src="https://github.com/user-attachments/assets/ae950f48-7b33-44ad-8221-ace619749f4f" />

### Simulate the Circuit
```
ngspice day2_nfet_idvgs_L015_W039.spice 
plot -vdd#branch
```

<img width="1919" height="992" alt="image" src="https://github.com/user-attachments/assets/0c28e0d6-1c60-4c1d-89a6-de3d5dd4829a" />

## Graph

<img width="1919" height="988" alt="image" src="https://github.com/user-attachments/assets/39747e0d-454f-499e-85af-9fd90a5bea37" />

- This graph shows the output characteristic for a single V_GS value of a PMOS transistor.
- The drain current (I_D) increases non-linearly with drain-source voltage (V_DS) at low V_DS, then becomes nearly linear at higher V_DS.
- The curve represents operation in the linear (ohmic) region, where the PMOS acts like a voltage-controlled resistor.

</details>

---

## Learning Outcomes

- Understand the basics of CMOS inverter operation and its voltage transfer characteristics (VTC).
- Learn about velocity saturation effects in scaled MOSFETs and their impact on device performance.
- Gain practical skills in running SPICE simulations for analyzing NMOS and PMOS characteristics.
- Interpret Id-Vds and Id-Vgs graphs to distinguish linear and saturation regions in MOSFETs.

**Workshop Status**: Day 2 Complete 

---
