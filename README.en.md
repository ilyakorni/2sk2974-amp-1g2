<p align="center">
  <a href="README.md">Русский</a> | <b>English</b> | <a href="README.de.md">Deutsch</a>
</p>

# 2sk2974-1g2-match: UHF 2SK2974 MOSFET Impedance Matching Network at 1.2 GHz

This repository contains the simulation project of a lumped-element reactive impedance matching network for the **2SK2974** UHF N-channel power MOSFET operating at **1.2 GHz (1200 MHz)**. The network matches the two-port active device to $50\ \Omega$ reference ports.

Circuit simulation was performed in **Cadence AWR Microwave Office (MWO)**. The repository contains the schematic, Smith chart locus plots, and frequency response curves for input reflection ($S_{11}$) and Voltage Standing Wave Ratio (VSWR).

---

## Project Structure

```text
├── cad/                                        # 3D CAD models and PCB layout files
│   └── .gitkeep
├── calculations/                               # Mathematical models (Mathcad, SMath)
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

## Transistor Parameters

The active device is modeled as a two-port network (`S2P_BLK ID=X1`). At the center frequency $f_0 = 1.2\text{ GHz}$, the scattering parameters referenced to $Z_0 = 50\ \Omega$ are:

$$\begin{aligned}
S_{11} &= 0.96198 \angle 178.34^\circ \\
S_{21} &= 0.19253 \angle 30.1079^\circ \\
S_{12} &= 0.03660 \angle 80.3997^\circ \\
S_{22} &= 0.93364 \angle -178.72^\circ
\end{aligned}$$

---

## Circuit Modeling

The electrical circuit simulation is configured in Cadence AWR Microwave Office.

<p align="center">
  <img src="docs/images/schematic_2sk2974_matching_1200mhz.png" alt="2SK2974 Matching Schematic" width="850"/>
  <br>
  <em>Figure 1 — Circuit schematic of the 2SK2974 matching network at 1.2 GHz in Cadence AWR MWO</em>
</p>

### Schematic Elements
- **RF Ports**: `PORT P=1` ($Z = 50\ \Omega$) and `PORT P=2` ($Z = 50\ \Omega$).
- **Input Matching Circuit**:
  - Shunt capacitor `CAP ID=C1`: $C = 21.8\text{ pF}$ (connected between Port 1 and ground).
  - Series inductor `IND ID=L1`: $L = 0.862\text{ nH}$ (between C1 node and transistor input).
- **Active Device**: `S2P_BLK ID=X1` with 2SK2974 scattering parameters at 1.2 GHz.
- **Output Matching Circuit**:
  - Series inductor `IND ID=L2`: $L = 1.4\text{ nH}$ (connected to transistor output).
  - Shunt capacitor `CAP ID=C2`: $C = 16.4\text{ pF}$ (connected between L2 output and ground before Port 2).

---

## Simulation Results

Numerical simulation was performed over the frequency sweep from $0$ to $2000\text{ MHz}$.

### Smith Chart Impedance Matching ($S_{11}$ and $S_{22}$)

<p align="center">
  <img src="docs/images/smith_chart_s11_matching.png" alt="S11 Smith Chart" width="48%"/>
  <img src="docs/images/smith_chart_s22_matching.png" alt="S22 Smith Chart" width="48%"/>
  <br>
  <em>Figure 2 — Smith chart trajectories for input S11 (left) and output S22 (right)</em>
</p>

At the target frequency of $1200\text{ MHz}$, both reflection loci pass through the normalized center of the Smith chart ($50\ \Omega$, normalized $1.0 + j0.0$).

### Input Reflection Response ($S_{11}$)

<p align="center">
  <img src="docs/images/plot_s11_frequency_response.png" alt="S11 Frequency Response" width="750"/>
  <br>
  <em>Figure 3 — Logarithmic magnitude of input reflection coefficient |S11| (dB) versus frequency</em>
</p>

- At $1200\text{ MHz}$, the marker records $|S_{11}| =$ **$-18.32\text{ dB}$**.
- The matching bandwidth for $|S_{11}| \le -10\text{ dB}$ spans from approximately $1130\text{ MHz}$ to $1240\text{ MHz}$.

### Voltage Standing Wave Ratio (VSWR)

<p align="center">
  <img src="docs/images/plot_vswr_frequency_response.png" alt="VSWR Frequency Response" width="750"/>
  <br>
  <em>Figure 4 — Voltage Standing Wave Ratio (VSWR) versus frequency</em>
</p>

- At $1204\text{ MHz}$, the marker records $\text{VSWR} =$ **$1.362$**.
- At the target frequency $1200\text{ MHz}$, the input match achieves $\text{VSWR} < 1.4$.

---

## License

Copyright (c) 2026 Ilya Kornilov

This source describes Open Hardware and is licensed under the CERN-OHL-P v2. 
You may redistribute and modify this source and make products using it under 
the terms of the CERN-OHL-P v2 (https://cern.ch/cern-ohl).

This source is distributed WITHOUT ANY EXPRESS OR IMPLIED WARRANTY, 
INCLUDING OF MERCHANTABILITY, SATISFACTORY QUALITY AND FITNESS FOR A 
PARTICULAR PURPOSE. Please see the CERN-OHL-P v2 for applicable conditions.
