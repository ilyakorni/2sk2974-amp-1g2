<p align="center">
  <a href="README.md">Русский</a> | <b>English</b> | <a href="README.de.md">Deutsch</a>
</p>

# 2sk2974-1g2-match: UHF 2SK2974 Power Amplifier Matching Network at 1.2 GHz

This repository contains the numerical simulation and layout design of an impedance matching network and power amplifier stage based on the **2SK2974** UHF N-channel power MOSFET operating at **1.2 GHz (1200 MHz)** with $50\ \Omega$ reference ports.

The project is developed in **Cadence AWR Microwave Office (MWO)** and includes:
1. Ideal lumped-element matching network (L-sections).
2. Distributed microstrip circuit model taking into account dielectric substrate parameters, bias feed stubs, and measured SMA connector transitions.
3. 2D Printed Circuit Board (PCB) layout.
4. Smith chart impedance loci, reflection coefficient ($S_{11}$), forward transmission gain ($S_{21}$), and Voltage Standing Wave Ratio (VSWR).

---

## Project Structure

```text
├── cad/                                        # 3D CAD models and PCB layout files
│   └── .gitkeep
├── calculations/                               # Mathematical models (Mathcad, SMath)
│   └── .gitkeep
├── docs/
│   └── images/                                 # Documentation figures, schematics, and simulation plots
│       ├── layout_pcb_amplifier_2sk2974.png    # 2D PCB layout of the amplifier in AWR MWO
│       ├── plot_microstrip_s21_gain.png        # Microstrip forward transmission gain |S21| plot
│       ├── plot_microstrip_vswr.png            # Microstrip Port 1 and Port 2 VSWR plot
│       ├── plot_s11_frequency_response.png     # |S11| (dB) vs frequency plot (ideal circuit)
│       ├── plot_s21_frequency_response.png     # |S21| (dB) transmission gain plot (ideal circuit)
│       ├── plot_vswr_frequency_response.png    # VSWR vs frequency plot (ideal circuit)
│       ├── schematic_2sk2974_matching_1200mhz.png # Ideal lumped matching circuit schematic
│       ├── schematic_microstrip_pa_2sk2974.png # Microstrip amplifier schematic with bias and SMA
│       ├── smith_chart_microstrip_s11_s22.png  # Smith chart S11 & S22 of microstrip circuit
│       ├── smith_chart_s11_matching.png        # Input S11 Smith chart (ideal circuit)
│       └── smith_chart_s22_matching.png        # Output S22 Smith chart (ideal circuit)
├── simulation/                                 # Cadence AWR Microwave Office project files
│   ├── amplifier_2sk2974_1200mhz_microstrip.emp # Project with microstrip schematic, SMA, and layout
│   └── transistor_2sk2974_matching_1200mhz.emp  # Project with ideal lumped elements
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

## Circuit Modeling & PCB Layout

### 1. Ideal Lumped-Element Matching Circuit

<p align="center">
  <img src="docs/images/schematic_2sk2974_matching_1200mhz.png" alt="2SK2974 Matching Schematic" width="850"/>
  <br>
  <em>Figure 1 — Circuit schematic of ideal 2SK2974 matching network at 1.2 GHz</em>
</p>

- **Ports**: `PORT P=1` ($Z = 50\ \Omega$) and `PORT P=2` ($Z = 50\ \Omega$).
- **Input Matching Circuit**: shunt capacitor `C1 = 21.8 pF` and series inductor `L1 = 0.862 nH`.
- **Output Matching Circuit**: series inductor `L2 = 1.4 nH` and shunt capacitor `C2 = 16.4 pF`.

### 2. Microstrip Circuit with Bias Networks and SMA Connectors

<p align="center">
  <img src="docs/images/schematic_microstrip_pa_2sk2974.png" alt="Microstrip Amplifier Schematic" width="850"/>
  <br>
  <em>Figure 2 — Microstrip amplifier schematic with bias decoupling stubs and measured SMA connector subcircuits</em>
</p>

- **Substrate Parameters (`MSUB ID=SUB1`)**:
  - Relative dielectric constant: $\varepsilon_r = 4.5$ (FR-4)
  - Substrate thickness: $H = 1.0\text{ mm}$
  - Copper cladding thickness: $T = 0.035\text{ mm}$ ($35\ \mu\text{m}$)
  - Dielectric loss tangent: $\tan\delta = 0.015$
  - Conductor bulk resistivity: $\rho = 0.0172$
- **RF Connectors**: subcircuits `SUBCKT NET="SMA_Measured_Thru"` representing measured S-parameters of end-launch SMA connectors.
- **DC-Blocking Capacitors**: `C2 = 10000 pF` ($10\text{ nF}$) and `C3 = 10000 pF` ($10\text{ nF}$) for input/output isolation.
- **Gate and Drain Bias Networks**: quarter-wave microstrip stubs `MLIN` ($W = 17.577\text{ mm}$, $L = 34.603\text{ mm}$) terminated in RF bypass capacitors `C5, C6 = 10000 pF` to ground.
- **Microstrip T-Junctions**: `MTEE` elements with branch widths $W_1 = W_2 = W_3 = 1\text{ mm}$.

### 3. PCB Layout

<p align="center">
  <img src="docs/images/layout_pcb_amplifier_2sk2974.png" alt="2SK2974 Amplifier PCB Layout" width="850"/>
  <br>
  <em>Figure 3 — 2D PCB microstrip layout generated in Cadence AWR Microwave Office</em>
</p>

The layout accommodates the power transistor mounting flange with screw holes for heatsink attachment, wide bias stubs, SMD passive pads, and end-launch SMA connector mounting pads.

---

## Simulation Results

### 1. Lumped-Element Model Results

<p align="center">
  <img src="docs/images/smith_chart_s11_matching.png" alt="S11 Smith Chart" width="48%"/>
  <img src="docs/images/smith_chart_s22_matching.png" alt="S22 Smith Chart" width="48%"/>
  <br>
  <em>Figure 4 — Smith chart reflection loci for ideal circuit: S11 (left) and S22 (right)</em>
</p>

<p align="center">
  <img src="docs/images/plot_s11_frequency_response.png" alt="S11 Frequency Response" width="48%"/>
  <img src="docs/images/plot_s21_frequency_response.png" alt="S21 Frequency Response" width="48%"/>
  <br>
  <em>Figure 5 — Frequency response of input reflection |S11| (left) and forward gain |S21| (right)</em>
</p>

<p align="center">
  <img src="docs/images/plot_vswr_frequency_response.png" alt="VSWR Frequency Response" width="700"/>
  <br>
  <em>Figure 6 — Voltage Standing Wave Ratio (VSWR) versus frequency for ideal model</em>
</p>

- Input reflection coefficient at $1200\text{ MHz}$: $|S_{11}| = -18.32\text{ dB}$.
- Forward transmission coefficient: $|S_{21}| = +6.141\text{ dB}$.
- Resonance VSWR: $\text{VSWR} \approx 1.27 - 1.36$ ($1.362$ at $1204\text{ MHz}$).

### 2. Microstrip Circuit Model Results (with Bias and SMA Transitions)

<p align="center">
  <img src="docs/images/smith_chart_microstrip_s11_s22.png" alt="Microstrip Smith Chart" width="600"/>
  <br>
  <em>Figure 7 — Smith chart impedance plot for microstrip circuit: S11 (triangle) and S22 (square) at 1.2 GHz</em>
</p>

<p align="center">
  <img src="docs/images/plot_microstrip_vswr.png" alt="Microstrip VSWR" width="48%"/>
  <img src="docs/images/plot_microstrip_s21_gain.png" alt="Microstrip Gain" width="48%"/>
  <br>
  <em>Figure 8 — Microstrip circuit performance at 1.2 GHz: Port 1 & 2 VSWR (left) and forward gain |S21| (right)</em>
</p>

- **Impedance Match on Smith Chart**: $S(1,1)$ and $S(2,2)$ at $1.2\text{ GHz}$ are positioned close to the normalized center ($50\ \Omega$).
- **Port VSWR**:
  - $\text{VSWR}_1$ (input port): **$1.442$**
  - $\text{VSWR}_2$ (output port): **$1.308$**
- **Forward Gain**: accounting for physical dielectric loss in FR-4 ($\tan\delta = 0.015$), conductor losses, and SMA transition discontinuities, forward transmission $|S_{21}|$ at $1.2\text{ GHz}$ is **$+4.710\text{ dB}$** ($4.7097\text{ dB}$).

---

## License

Copyright (c) 2026 Ilya Kornilov

This source describes Open Hardware and is licensed under the CERN-OHL-P v2. 
You may redistribute and modify this source and make products using it under 
the terms of the CERN-OHL-P v2 (https://cern.ch/cern-ohl).

This source is distributed WITHOUT ANY EXPRESS OR IMPLIED WARRANTY, 
INCLUDING OF MERCHANTABILITY, SATISFACTORY QUALITY AND FITNESS FOR A 
PARTICULAR PURPOSE. Please see the CERN-OHL-P v2 for applicable conditions.
