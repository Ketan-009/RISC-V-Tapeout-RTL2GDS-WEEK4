# Day 1: Introduction to Spice and NMOS basics

This comprehensive guide covers basic of NMOS, Drain Current (Id) vs Drain-to-Source Voltage (Vds), Introduction to Circuit design and Spice simulations.

**Note:** Click the sections below to expand.

---

## Table of Contents

<details>
<summary>Introduction to Spice and NMOS basic</summary>

## NMOS Transistor: Structure, Operation, and Models

Concise, topic-wise notes on NMOS transistor fundamentals for easy reference in a repository.

---

### 1) NMOS Device Structure

Description:
- An NMOS transistor is a four-terminal device (Gate, Drain, Source, Body) built on a p-type substrate.

Key Components:
- P-Substrate: Base material (p-type silicon).
- N⁺ Source/Drain: Heavily doped n-type regions for current conduction.
- Gate Oxide (SiO₂): Thin insulating layer for capacitive control.
- Gate Electrode: Poly-Si or metal; controls inversion layer formation.
- Isolation Oxide: Prevents electrical cross-talk between devices.
- Body Terminal (B): Connected to the substrate, usually tied to ground.

Operation Principle:
- Applying a gate-to-source voltage such that V_GS > V_t forms a conductive channel between source and drain under the gate oxide.

<img width="975" height="397" alt="image" src="https://github.com/user-attachments/assets/55484323-790b-4b6f-893f-aa83f0baac04" />

---

### 2) First-Order MOSFET I–V Relationships

Charge and Current Modeling:
- Channel charge per unit area:
  $$Q_i(x) = -C_{ox}\left([V_{GS}-V(x)] - V_t\right)$$

Drift Current:
- For long-channel, first-order model (ignoring velocity saturation):
  $$I_D = \mu_n C_{ox}\frac{W}{L}\left([V_{GS}-V_t]V_{DS} - \frac{V_{DS}^2}{2}\right)$$

Region Validity:
- Linear (Triode) region holds for:
  $$V_{DS} < (V_{GS} - V_t)$$

Parameters:
- μ_n: Electron mobility
- C_ox: Oxide capacitance per unit area
- W/L: Device aspect ratio
- Process transconductance parameter:
  $$k_n' = \mu_n C_{ox}$$

<img width="975" height="485" alt="image" src="https://github.com/user-attachments/assets/27c7c5af-d677-484c-b23e-2d66c98fe332" />

<img width="975" height="548" alt="image" src="https://github.com/user-attachments/assets/31c508f4-9ad6-4bb3-991b-efe310396933" />

---

### 3) Power-Aware Cell Characterization

Purpose:
- Obtain delay lookup tables for standard buffers under various input slews and output loads.

Table Dimensions:
- Rows: Input slew rates (e.g., 20–80 ps)
- Columns: Output capacitances (e.g., 10–110 fF)

Applications:
- Buffer sizing and insertion
- Timing-driven synthesis and power optimization in VLSI

Key Insight:
- Identical buffers driving identical loads ensure consistent delay characteristics.

<img width="975" height="531" alt="image" src="https://github.com/user-attachments/assets/6b06fb06-a1e7-4641-bbd0-48910dc2c163" />

---

### 4) SPICE Setup and Device Equations

(a) Threshold Voltage Equation
- Body-effect-aware threshold voltage:
  $$V_t = V_{t0} + \gamma\left(\sqrt{\lvert -2\Phi_f + V_{SB}\rvert} - \sqrt{\lvert -2\Phi_f\rvert}\right)$$

Where:
- Body-effect coefficient (typical form):
  $$\gamma = \frac{\sqrt{2q\varepsilon_{si}N_A}}{C_{ox}}$$
- Fermi potential:
  $$\Phi_f = -\Phi_T \ln\left(\frac{N_A}{n_i}\right)$$

Insight:
- V_t increases with positive V_SB (body effect).

(b) Drain Current Models
- Linear Region:
  $$I_D = k_n\left([V_{GS}-V_t]V_{DS} - \frac{V_{DS}^2}{2}\right), \quad \text{with } k_n = k_n'\frac{W}{L}$$
- Saturation Region (with channel-length modulation λ):
  $$I_D = \frac{k_n'}{2}\frac{W}{L}(V_{GS}-V_t)^2\,[1+\lambda V_{DS}]$$

SPICE:
- Uses these equations (with additional effects in modern models) for DC and transient analysis via numerical iteration.

<img width="975" height="481" alt="image" src="https://github.com/user-attachments/assets/6861e4a0-49e6-4d37-ba10-2e8cb3b60b68" />

---

### 5) Threshold Voltage and Resistive Region Operation

Threshold Definition:
- V_t is the gate voltage causing strong inversion beneath the gate oxide.

Linear Operation Example:
- Given V_GS = 1 V, V_t = 0.45 V, V_DS = 0.05 V:
  - The device operates in the linear region and behaves as a voltage-controlled resistor.

Key Relation:
- Channel resistance in linear region:
  $$R_{DS} \propto \frac{1}{(V_{GS}-V_t)}$$

Applications:
- Analog switches, transmission gates.

---



</details>

<details>
<summary>Practical Labs Implementation</summary>

### Setup

Clone the repo

```
git clone https://github.com/kunalg123/sky130CircuitDesignWorkshop.git
```

<img width="1919" height="723" alt="image" src="https://github.com/user-attachments/assets/169e9d81-40d4-4058-b813-24ddfcd75f9e" />

### Spice Simulation code

```bash
*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description

XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=5 l=2
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

<img width="1577" height="742" alt="image" src="https://github.com/user-attachments/assets/d6c14415-5044-4b00-85d7-66c68b327283" />

### Simulate the Circuit
```
ngspice day1_nfet_idvds_L2_W5.spice 
plot -vdd#branch
```

<img width="1919" height="1020" alt="image" src="https://github.com/user-attachments/assets/b8fe785e-a465-4d8f-8334-c1f6af77ffc4" />

- The plot shows the output characteristics of an NMOS transistor (I_D vs. V_DS for various V_GS values).
- At low V_DS, the curves are linear (ohmic/triode region); at high V_DS, they flatten (saturation/active region).
- Increasing V_GS increases the drain current (I_D) for the same V_DS, as shown by higher curves.

</details>

## Learning Outcomes

- Understand the physical structure and terminals of an NMOS transistor.
- Apply first-order equations to model NMOS current-voltage characteristics.
- Use cell characterization to optimize timing and power in VLSI circuits.
- Analyze NMOS behavior and parameters using SPICE device equations.
- Recognize and utilize NMOS operation in the linear (resistive) region for analog applications.

---

**Workshop Status**: Day 1 Complete 
---
