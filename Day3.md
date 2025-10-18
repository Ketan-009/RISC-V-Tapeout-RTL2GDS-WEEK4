# Day 3: CMOS Switching Threshold and Dynamic Simulations

An overview of creating and simulating a CMOS inverter using the Sky130 PDK in Ngspice, covering netlist setup, static VTC, switching threshold (Vm), and timing analysis.

**Note:** Click the sections below to expand.

---

## Table of Contents

<details>
<summary>Theory</summary>

# SPICE Simulations and CMOS Inverter Analysis

---

## 1. SPICE Deck Creation for CMOS Inverter
**Objective:** Define PMOS and NMOS models, supply rails, input source, and output load.
- **Netlist Components:**
  - `.model` statements for PMOS/NMOS
  - Device instances: `M1 out in vdd vdd pmos W=... L=...`
  - DC voltage source for input sweep
  - Capacitive load at output (e.g., 10 fF)
- **Key Parameters:** Channel lengths, widths, and supply voltage (e.g., 2.5 V).

<img width="1888" height="922" alt="Screenshot 2025-10-18 193344" src="https://github.com/user-attachments/assets/f6dfefc7-eba6-49ec-b656-f107ae3d191b" />

---

## 2. SPICE Simulation for CMOS Inverter
**Procedure:** Run DC sweep of input voltage from 0 V to VDD.
- **Voltage Transfer Characteristic (VTC):** Plot Vout vs. Vin.
- **Identify Region:** Transition region where output falls from VDD to 0.

<img width="1819" height="855" alt="Screenshot 2025-10-18 195049" src="https://github.com/user-attachments/assets/5c06f65b-c736-4a05-95a2-cd27fff732d0" />

<img width="1803" height="952" alt="Screenshot 2025-10-18 195110" src="https://github.com/user-attachments/assets/6663aecc-13e0-4cea-8991-da5ac0cf57a1" />

---

## 3. Sky130 Labs SPICE Simulation
**Context:** Use the Sky130 PDK models.
- **Model Files:** Include `sky130.lib.spice` definitions.
- **Run Commands:** `.include` PDK, `.dc Vin 0 2.5 0.01`, `.plot V(out) V(in)`.


---

## 4. Static Behavior Evaluation: Switching Threshold (Vm)
**Definition:** Vm is the input voltage at which Vin = Vout on the VTC.
- **Extraction:** Plot the VTC and the line Vout = Vin; intersection gives Vm.
- **Example Values:** Vm ≈ 0.98 V when Wp/Lp = Wn/Ln; Vm ≈ 1.2 V for larger PMOS sizing.

<img width="1839" height="959" alt="Screenshot 2025-10-18 200041" src="https://github.com/user-attachments/assets/7787f502-7a2d-4977-b8ab-0e9be95b7b0c" />

---

## 5. Analytical Expression of Vm
**MOS Currents at Vm:** I_{dsp} + I_{dsn} = 0 (pull-up equals pull-down).
- **Approximation in Saturation:**
  \[
    k_p\,(V_m - V_{dd} - V_{tp})\,V_{dsat,p} - \tfrac{V_{dsat,p}^2}{2}
    + k_n\,(V_m - V_{tn})\,V_{dsat,n} - \tfrac{V_{dsat,n}^2}{2} = 0.
  \]
- **Closed-Form:**
  \[
    V_m = \frac{R\,V_{dd}}{1 + R},\quad R = \frac{k_p\,V_{dsat,p}}{k_n\,V_{dsat,n}}.
  \]
- **Dependence:** Vm scales with ratio of (W/L)_p to (W/L)_n.

<img width="1750" height="674" alt="Screenshot 2025-10-18 200217" src="https://github.com/user-attachments/assets/3fff0429-8afc-4657-9031-49d4fb8735ea" />

---

## 6. Inverse Analytical Expression: Sizing from Desired Vm
**Goal:** Determine transistor sizing for target switching threshold.
- **Solve:** R = V_m / (V_{dd} - V_m).
- **Compute (W/L)p = R\,(W/L)_n\,(k_n\,V_{dsat,n}/{k_p\,V_{dsat,p}}).

---

## 7. Static and Dynamic Simulation of CMOS Inverter
**Metrics:**
- **Rise Time (t_r):** Time for output to go from 10% to 90% VDD.
- **Fall Time (t_f):** Time for output to go from 90% to 10% VDD.
- **Propagation Delays (t_{PLH}, t_{PHL}):** Measured at Vm.
- **Impact of Sizing:** Increased PMOS width reduces t_r but may shift Vm.

<img width="1871" height="1016" alt="image" src="https://github.com/user-attachments/assets/d0ab8511-3946-4a5d-ae8d-3b8d4f970919" />

---

## 8. Applications in Clock Network and Static Timing Analysis (STA)
**Clock Buffer Chain:** Sequence of inverters/buffers to distribute clock.
- **Setup Analysis:** For 1 GHz clock (T = 1 ns), ensure
  \[
    \theta + \Delta_1 < T + \Delta_2 + 3t_d - S - SU,
  \]
  where SU is setup margin, S is skew, \Delta are delay variations.
- **Role of Inverter Sizing:** Tailor rise/fall delays to balance clock skew and insertion delay.

---

</details>

<details>
<summary>Practical Labs Implementation</summary>

## Plotting VTC Characterstics

### Spice Simulation code

```
gedit day3_inv_vtc_Wp084_Wn036.spice
```

```bash
*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description


XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=0.84 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15


Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op

.dc Vin 0 1.8 0.01

.control
run
setplot dc1
display
.endc

.end
```

<img width="1919" height="754" alt="image" src="https://github.com/user-attachments/assets/5026c388-cc74-4203-8c06-e32cb58fb906" />


### Simulate the Circuit
```
ngspice day3_inv_vtc_Wp084_Wn036.spice 
plot out vs in
```

<img width="1919" height="988" alt="image" src="https://github.com/user-attachments/assets/7f757deb-4104-4005-9fae-f5659ab9c062" />

## Graph

<img width="1919" height="991" alt="image" src="https://github.com/user-attachments/assets/1246d774-8cf8-4d33-bc31-7988111a6882" />

- This graph shows the Voltage Transfer Characteristic (VTC) of a CMOS inverter.
- As input voltage increases from 0 to VDD, the output quickly switches from high (VDD) to low (GND) at the threshold region.
- The sharp transition indicates strong noise margins and good inverter switching behavior.

## Plotting Trans Characterstics

### Spice Simulation code

```
gedit day3_inv_tran_Wp084_Wn036.spice
```

```bash
*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description


XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=0.84 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15


Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 PULSE(0V 1.8V 0 0.1ns 0.1ns 2ns 4ns)

*simulation commands

.tran 1n 10n

.control
run
.endc

.end
```

<img width="1919" height="697" alt="image" src="https://github.com/user-attachments/assets/23ce114a-3464-4db5-8e64-e04be8c9f5ea" />

### Simulate the Circuit
```
ngspice day3_inv_tran_Wp084_Wn036.spice 
plot out vs time in
```

<img width="1919" height="933" alt="image" src="https://github.com/user-attachments/assets/d24f8360-c741-478e-8a49-fc057d5db106" />

## Graph

<img width="1919" height="990" alt="image" src="https://github.com/user-attachments/assets/230f4791-d4d8-40ff-8816-150faa780c2c" />

- The plot shows the transient response of a CMOS inverter to a square wave input.
- When the input (blue) goes high, the output (red) switches low, and vice versa, demonstrating inverter action.
- The transitions are sharp, indicating fast switching and good signal integrity.

</details>

---

## Learning Outcomes

- Learn how to set up and simulate a CMOS inverter using Sky130 PDK in Ngspice.
- Understand and extract the inverter switching threshold (Vm) from the VTC curve.
- Analyze the effect of device sizing on Vm, rise/fall times, and propagation delays.
- Gain practical skills in performing static (VTC) and dynamic (transient) SPICE simulations for timing analysis.

**Workshop Status**: Day 3 Complete 

---
