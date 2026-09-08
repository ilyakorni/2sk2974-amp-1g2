<p align="center">
  <a href="README.md">Русский</a> | <b>English</b> | <a href="README.de.md">Deutsch</a>
</p>

# 2sk2974-1g2-match: Complex Impedance Matching of 2SK2974 UHF MOSFET Two-Port Network at 1.2 GHz

This repository contains the design, analytical synthesis, and numerical simulation of a lumped-element reactive impedance matching network for the **2SK2974** UHF N-channel power MOSFET operating at **1.2 GHz (1200 MHz)**. The matching network transforms the low-impedance gate and drain ports of the active device to standard $50\ \Omega$ reference ports.

The project includes analytical impedance transformation derivations, circuit-level simulation in **Cadence AWR Microwave Office (MWO)**, Smith chart locus plots, and frequency response curves for input return loss ($S_{11}$) and Voltage Standing Wave Ratio (VSWR).

---

## Project Structure

```text
├── cad/                                        # 3D CAD models and PCB layout files
│   └── .gitkeep
├── calculations/                               # Mathematical models (Mathcad, SMath, analytics)
│   └── .gitkeep
├── docs/
│   └── images/                                 # Documentation figures, schematics, and simulation plots
│       ├── plot_s11_frequency_response.png     # |S11| (dB) vs frequency plot
│       ├── plot_vswr_frequency_response.png    # VSWR vs frequency plot
│       ├── schematic_2sk2974_matching_1200mhz.png # AWR Microwave Office schematic diagram
│       ├── smith_chart_s11_matching.png        # Input S11 Smith chart impedance locus
│       └── smith_chart_s22_matching.png        # Output S22 Smith chart impedance locus
├── simulation/                                 # Numerical simulation workspace
│   └── transistor_2sk2974_matching_1200mhz.emp # Cadence AWR Microwave Office project file
├── .gitignore                                  # Git exclusion rules for CAD and OS artifacts
├── LICENSE                                     # Full text of CERN-OHL-P v2 license
├── README.de.md                                # German documentation
├── README.en.md                                # English documentation
└── README.md                                   # Russian documentation
```

---

## Analytical Calculation

The active transistor is modeled as a two-port network characterized at $f_0 = 1.2\text{ GHz}$ by the following scattering ($S$) parameters referenced to $Z_0 = 50\ \Omega$:

$$\begin{aligned}
S_{11} &= 0.96198 \angle 178.34^\circ \\
S_{21} &= 0.19253 \angle 30.1079^\circ \\
S_{12} &= 0.03660 \angle 80.3997^\circ \\
S_{22} &= 0.93364 \angle -178.72^\circ
\end{aligned}$$

### Transistor Characteristic Port Impedances

The intrinsic gate and drain complex impedances are evaluated via bilinear conformal mapping:

$$Z_{in} = Z_0 \frac{1 + S_{11}}{1 - S_{11}} \approx 0.9691 + j0.7241\ \Omega$$

$$Z_{out} = Z_0 \frac{1 + S_{22}}{1 - S_{22}} \approx 1.7161 - j0.5579\ \Omega$$

Both ports exhibit sub-ohm to low-ohm real parts ($R_{in} < 1\ \Omega$, $R_{out} < 2\ \Omega$), which is characteristic of high-power RF MOSFET structures at UHF frequencies. The objective of the matching network is to transform $50\ \Omega$ termination impedances into the required source and load impedances with low insertion loss.

### L-Section Reactive Matching Network Synthesis

Because $R_0 = 50\ \Omega > R_{in}, R_{out}$, the matching topology requires shunt reactive elements adjacent to the $50\ \Omega$ ports and series reactive elements adjacent to the low-impedance transistor terminals:
1. **Input Network (Port 1 $\rightarrow$ Gate)**:
   - Shunt capacitor $C_1 = 21.8\text{ pF}$ across the $50\ \Omega$ source port to lower the real impedance level.
   - Series inductor $L_1 = 0.862\text{ nH}$ in series with the gate to tune out residual susceptance and achieve conjugate impedance match.
2. **Output Network (Drain $\rightarrow$ Port 2)**:
   - Series inductor $L_2 = 1.400\text{ nH}$ directly connected to the drain terminal.
   - Shunt capacitor $C_2 = 16.4\text{ pF}$ across the $50\ \Omega$ output port.

### ABCD Transfer Matrix Cascade

The cascaded network is evaluated using 2x2 transmission matrices:

$$[M_{total}] = \begin{bmatrix} 1 & 0 \\ j\omega C_1 & 1 \end{bmatrix} \begin{bmatrix} 1 & j\omega L_1 \\ 0 & 1 \end{bmatrix} [M_{trans}] \begin{bmatrix} 1 & j\omega L_2 \\ 0 & 1 \end{bmatrix} \begin{bmatrix} 1 & 0 \\ j\omega C_2 & 1 \end{bmatrix}$$

Theoretical computation at $1.2\text{ GHz}$:
- Input reflection coefficient: $|S_{11}| = 0.1205$ ($-18.38\text{ dB}$)
- Output reflection coefficient: $|S_{22}| = 0.1211$ ($-18.34\text{ dB}$)
- Forward transmission coefficient: $|S_{21}| = 2.0278$ ($+6.14\text{ dB}$)
- Voltage Standing Wave Ratio: $\text{VSWR}_1 = 1.274$, $\text{VSWR}_2 = 1.276$

---

## Circuit Modeling

The electrical circuit simulation is configured in Cadence AWR Microwave Office.

<p align="center">
  <img src="docs/images/schematic_2sk2974_matching_1200mhz.png" alt="2SK2974 Matching Schematic" width="850"/>
  <br>
  <em>Figure 1 — Circuit schematic of the 2SK2974 matching network at 1.2 GHz in Cadence AWR MWO</em>
</p>

### Schematic Details
- **RF Terminations**: Unbalanced $50\ \Omega$ ports `PORT P=1` and `PORT P=2`.
- **Transistor Model**: S-parameter 2-port block `S2P_BLK ID=X1` with 2SK2974 scattering parameters.
- **Input Circuit**: Shunt capacitor `CAP ID=C1` ($C = 21.8\text{ pF}$) and series inductor `IND ID=L1` ($L = 0.862\text{ нГн}$).
- **Output Circuit**: Series inductor `IND ID=L2` ($L = 1.4\text{ nH}$) and shunt capacitor `CAP ID=C2` ($C = 16.4\text{ pF}$).

---

## Simulation Results

The numerical response was computed over the frequency sweep of $0$ to $2000\text{ MHz}$.

### Smith Chart Impedance Matching ($S_{11}$ and $S_{22}$)

The normalized complex reflection coefficients are displayed on the Smith charts below.

<p align="center">
  <img src="docs/images/smith_chart_s11_matching.png" alt="S11 Smith Chart" width="48%"/>
  <img src="docs/images/smith_chart_s22_matching.png" alt="S22 Smith Chart" width="48%"/>
  <br>
  <em>Figure 2 — Smith chart trajectories for input S11 (left) and output S22 (right) matching networks</em>
</p>

At the target frequency of $1200\text{ MHz}$, both reflection loci converge directly at the normalized center of the Smith chart ($z \approx 1.0 + j0.0$), demonstrating near-zero reactive reflection and close resistance matching to the $50\ \Omega$ system.

### Input Reflection Response ($S_{11}$)

<p align="center">
  <img src="docs/images/plot_s11_frequency_response.png" alt="S11 Frequency Response" width="750"/>
  <br>
  <em>Figure 3 — Logarithmic magnitude of input reflection coefficient |S11| (dB) versus frequency</em>
</p>

- At $1200\text{ MHz}$, a sharp matching notch reaches **$-18.32\text{ dB}$** (reflected power $< 1.5\%$).
- The operational matching bandwidth for $|S_{11}| \le -10\text{ dB}$ spans from $1130\text{ MHz}$ to $1240\text{ MHz}$ ($\Delta f \approx 110\text{ MHz}$, fractional bandwidth $\approx 9.2\%$).

### Voltage Standing Wave Ratio (VSWR)

<p align="center">
  <img src="docs/images/plot_vswr_frequency_response.png" alt="VSWR Frequency Response" width="750"/>
  <br>
  <em>Figure 4 — Voltage Standing Wave Ratio (VSWR) versus frequency</em>
</p>

- The standing wave ratio at the center frequency attains **$1.27 - 1.36$** (the marker records $\text{VSWR} = 1.362$ at $1204\text{ MHz}$).
- The acceptable match criterion ($\text{VSWR} \le 2.0$) is satisfied across the entire operational channel around $1.2\text{ GHz}$.

### Numerical Verification and Model Convergence

| Metric | Analytical Calculation (ABCD) | Numerical Simulation (AWR MWO) | Difference |
| :--- | :---: | :---: | :---: |
| Center Frequency $f_0$ | $1200\text{ MHz}$ | $1200\text{ MHz}$ | $0.0\%$ |
| Input Reflection $\|S_{11}\|$ | $-18.38\text{ dB}$ | $-18.32\text{ dB}$ | $0.06\text{ dB}$ |
| Output Reflection $\|S_{22}\|$ | $-18.34\text{ dB}$ | $-18.30\text{ dB}$ | $0.04\text{ dB}$ |
| Input Standing Wave Ratio ($\text{VSWR}_1$) | $1.274$ | $1.28 - 1.36$ | within tolerance |

The numerical data from Cadence AWR Microwave Office shows high convergence with the analytical matrix formulation.

---

## License

Copyright (c) 2026 Ilya Kornilov

This source describes Open Hardware and is licensed under the CERN-OHL-P v2. 
You may redistribute and modify this source and make products using it under 
the terms of the CERN-OHL-P v2 (https://cern.ch/cern-ohl).

This source is distributed WITHOUT ANY EXPRESS OR IMPLIED WARRANTY, 
INCLUDING OF MERCHANTABILITY, SATISFACTORY QUALITY AND FITNESS FOR A 
PARTICULAR PURPOSE. Please see the CERN-OHL-P v2 for applicable conditions.
