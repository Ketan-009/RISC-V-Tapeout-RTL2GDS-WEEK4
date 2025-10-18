# Day 5: Power Supply and Device Variation in CMOS Inverter Robustness

Noise margin quantifies inverter robustness against input disturbances. It defines voltage ranges where logic levels remain valid.

**Note:** Click the sections below to expand.

---

## Table of Contents

<details>
<summary>Power Supply Variation</summary>

---

## 1. Smart SPICE Simulation for Supply Scaling

- **Objective:** Analyze VTC shifts and gain changes when VDD scales from 2.5 V to 1 V with fixed device sizing (Wn=0.375 µm, Wp=0.9375 µm). 
- **Result:** Lower supply reduces VTC range, degrades slope and static gain (|gain| drops, ~56% reduction).

<img width="1870" height="500" alt="Screenshot 2025-10-18 223859" src="https://github.com/user-attachments/assets/1988b321-88d4-423c-bb90-cd33a8faf92f" />

---

## 2. Advantages & Disadvantages of Low VDD

- **Advantages:**
  - Lower dynamic power (∝ VDD²).
  - Reduced electric field stress.
    
- **Disadvantages:**
  - Degraded noise margins and static gain.
  - Slower switching due to reduced overdrive.

<img width="1782" height="1006" alt="Screenshot 2025-10-18 224037" src="https://github.com/user-attachments/assets/c6a3bb1c-ba22-4a05-b44b-e4f24bb91c99" />
<img width="1778" height="1034" alt="Screenshot 2025-10-18 224127" src="https://github.com/user-attachments/assets/69abe248-e7df-44ff-9cfb-7696f6f62f95" />

---

## 3. Sources of Device Variation

- **Etching Process:** Variability in channel length (L) due to etch nonuniformity.
- **Oxide Thickness (Tox):** Variations affect threshold voltage (Vt).

<img width="1111" height="540" alt="image" src="https://github.com/user-attachments/assets/0c925e49-449a-40a4-8977-ea52aa968899" />

---

## 4. Smart SPICE Simulation for Device Variations
- **Approach:** Monte Carlo or worst-case corner analyses on L and Tox.
- **Metrics:** Spread in VTC, threshold (Vm), delay, and noise margins.


---

## 5. Impact & Mitigation
- **Impact:** Device variations shift VTC, degrade yield and timing.
- **Mitigation:**  
  1. **Design Margins:** Add guardbands on timing and margins.  
  2. **Adaptive Biasing:** Compensate Vt shifts.  
  3. **Statistical STA:** Include variation in timing analysis.


---


</details>

<details>
<summary>Practical Labs Implementation</summary>

## Observing Power Supply Variation

### Spice Simulation code

```
gedit day5_inv_supplyvariation_Wp1_Wn036.spice
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

.control

let powersupply = 1.8
alter Vdd = powersupply
	let voltagesupplyvariation = 0
	dowhile voltagesupplyvariation < 6
	dc Vin 0 1.8 0.01
	let powersupply = powersupply - 0.2
	alter Vdd = powersupply
	let voltagesupplyvariation = voltagesupplyvariation + 1
      end
 
plot dc1.out vs in dc2.out vs in dc3.out vs in dc4.out vs in dc5.out vs in dc6.out vs in xlabel "input voltage(V)" ylabel "output voltage(V)" title "Inveter dc characteristics as a function of supply voltage"

.endc

.end
```

<img width="1919" height="777" alt="image" src="https://github.com/user-attachments/assets/11c5c3bf-1a76-4314-a2e2-ce48adb0cfc8" />


### Simulate the Circuit
```
ngspice day5_inv_supplyvariation_Wp1_Wn036.spice
```

<img width="1919" height="949" alt="image" src="https://github.com/user-attachments/assets/4d49b91c-3ef8-4836-aeb5-e5864de47c19" />

## Graph

<img width="1919" height="994" alt="image" src="https://github.com/user-attachments/assets/de042c04-e755-4357-b998-7c235020ddb7" />

- The graph shows CMOS inverter voltage transfer characteristics (VTC) for different supply voltages.
- As supply voltage decreases, both the output swing and switching threshold shift lower.
- Multiple colored curves represent the inverter response at varying VDD values, demonstrating how VTC depends on supply voltage.

## Observing Power Supply Variation

### Spice Simulation code

```
gedit day5_inv_supplyvariation_Wp1_Wn036.spice
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

.control

let powersupply = 1.8
alter Vdd = powersupply
	let voltagesupplyvariation = 0
	dowhile voltagesupplyvariation < 6
	dc Vin 0 1.8 0.01
	let powersupply = powersupply - 0.2
	alter Vdd = powersupply
	let voltagesupplyvariation = voltagesupplyvariation + 1
      end
 
plot dc1.out vs in dc2.out vs in dc3.out vs in dc4.out vs in dc5.out vs in dc6.out vs in xlabel "input voltage(V)" ylabel "output voltage(V)" title "Inveter dc characteristics as a function of supply voltage"

.endc

.end
```

<img width="1919" height="777" alt="image" src="https://github.com/user-attachments/assets/11c5c3bf-1a76-4314-a2e2-ce48adb0cfc8" />


### Simulate the Circuit
```
ngspice day5_inv_supplyvariation_Wp1_Wn036.spice
```

<img width="1919" height="949" alt="image" src="https://github.com/user-attachments/assets/4d49b91c-3ef8-4836-aeb5-e5864de47c19" />

## Graph

<img width="1919" height="994" alt="image" src="https://github.com/user-attachments/assets/de042c04-e755-4357-b998-7c235020ddb7" />

- The graph shows CMOS inverter voltage transfer characteristics (VTC) for different supply voltages.
- As supply voltage decreases, both the output swing and switching threshold shift lower.
- Multiple colored curves represent the inverter response at varying VDD values, demonstrating how VTC depends on supply voltage.

## Observing Device Supply Variation

### Spice Simulation code

```
gedit day5_inv_devicevariation_wp7_wn042.spice
```

```bash
*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description


XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=7 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.42 l=0.15


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

<img width="1919" height="778" alt="image" src="https://github.com/user-attachments/assets/a17a9f6c-feff-4e5c-964a-ab66783b70d9" />


### Simulate the Circuit
```
ngspice day5_inv_devicevariation_wp7_wn042.spice
```

<img width="1919" height="865" alt="image" src="https://github.com/user-attachments/assets/7a837795-0cf2-4362-8726-d824bee620ba" />

## Graph

<img width="1919" height="912" alt="image" src="https://github.com/user-attachments/assets/f3b55736-6c04-460f-8bcd-3453b6f2cd13" />

- The image displays the Voltage Transfer Characteristic (VTC) of a CMOS inverter.
- The output voltage (Vout) stays high when the input (Vin) is low, then sharply transitions to low as Vin increases past the switching threshold.
- The steep slope indicates strong noise margins and reliable digital switching.

---

</details>

## Learning Outcomes

- Understand how CMOS inverter VTC and gain are affected by changes in power supply voltage.
- Analyze the trade-offs of low VDD operation, including power savings versus degraded noise margins and speed.
- Learn about fabrication-induced device variations and their impact on inverter performance and yield.
- Gain practical skills in SPICE simulations to observe effects of supply and device variation on inverter robustness.

**Workshop Status**: Day 5 Complete 

---
