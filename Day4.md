# Day 4: Noise Margin Analysis for CMOS Inverter

Noise margin quantifies inverter robustness against input disturbances. It defines voltage ranges where logic levels remain valid.

**Note:** Click the sections below to expand.

---

## Table of Contents

<details>
<summary>Introduction to Noise Margin</summary>

---

## 1. Noise Margin Voltage Parameters
- **V_IL**: Maximum input voltage recognized as logic '0'.
- **V_IH**: Minimum input voltage recognized as logic '1'.
- **V_OL**: Maximum output voltage for logic '0'.
- **V_OH**: Minimum output voltage for logic '1'.

<img width="1371" height="870" alt="Screenshot 2025-10-18 212729" src="https://github.com/user-attachments/assets/5ba3a50b-be16-4ec7-88e5-f43055c6f5d9" />

---

## 2. Noise Margin Definitions
- **N_ML (Noise Margin Low)**: N_ML = V_IL - V_OL.
- **N_MH (Noise Margin High)**: N_MH = V_OH - V_IH.

<img width="1690" height="880" alt="Screenshot 2025-10-18 212915" src="https://github.com/user-attachments/assets/7ec22954-6a1d-467f-9b00-ae1d44dbc0a4" />

---

## 3. Noise Margin Extraction
**Procedure:** Overlay VTC with Vout=Vin line and horizontal V_IL/V_IH markers. Intersection gives V_IL and V_IH.


---

## 4. Noise Margin Equation and Summary
- **Equations:**
  - N_ML = V_IL - V_OL
  - N_MH = V_OH - V_IH
- **Summary:** Higher transistor sizing improves N_MH but may reduce N_ML.

<img width="1574" height="819" alt="Screenshot 2025-10-18 213117" src="https://github.com/user-attachments/assets/f768e7b3-2082-4b7c-b886-20de45ddf157" />

---

## 5. Noise Margin Variation with PMOS Width
Increasing W_p/L_p shifts VTC right, raising V_IH and V_OH, thus:
- **N_MH** increases
- **N_ML** decreases

<img width="1850" height="976" alt="Screenshot 2025-10-18 213209" src="https://github.com/user-attachments/assets/bfd736a0-f19b-4ac8-af81-923505d53e82" />

---


</details>

<details>
<summary>Practical Labs Implementation</summary>

## Plotting VTC Characterstics

### Spice Simulation code

```
gedit day4_inv_noisemargin_wp1_wn036.spice
```

```bash
*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description


XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=1 l=0.15
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

<img width="1919" height="745" alt="image" src="https://github.com/user-attachments/assets/9e3caa55-34b3-48ce-a923-c5cf2d203ff6" />


### Simulate the Circuit
```
ngspice day4_inv_noisemargin_wp1_wn036.spice
plot out vs in
```

<img width="1919" height="941" alt="image" src="https://github.com/user-attachments/assets/aa192007-a536-418f-81d7-e7cdc9d24227" />

## Graph

<img width="1919" height="992" alt="image" src="https://github.com/user-attachments/assets/2d4c7a29-d14a-4b44-b2b5-58f845116f5e" />

- This graph displays the Voltage Transfer Characteristic (VTC) of a CMOS inverter.
- The output voltage drops sharply from high (VDD) to low (GND) as the input crosses the inverter’s switching threshold.
- The steep transition indicates strong noise margins and reliable digital switching behavior.

</details>

---

## Learning Outcomes

- Understand the concept of noise margin and its importance for CMOS inverter reliability.
- Learn to identify and calculate V_IL, V_IH, V_OL, and V_OH from the inverter VTC.
- Extract and compute noise margin values (N_ML, N_MH) using graphical and analytical methods.
- Analyze how transistor sizing affects the noise margins and overall inverter robustness.

**Workshop Status**: Day 4 Complete 

---
