<p align="center">
  <a href="README.md">Русский</a> | <a href="README.en.md">English</a> | <b>Deutsch</b>
</p>

# 2sk2974-1g2-match: UHF-Leistungsverstärker-Anpassungsnetzwerk mit 2SK2974 bei 1,2 GHz

Dieses Repository enthält die numerische Simulation und das Layout-Design eines Anpassungsnetzwerks und einer Verstärkerstufe auf Basis des **2SK2974** UHF-N-Kanal-Leistungs-MOSFETs bei einer Betriebsfrequenz von **1,2 GHz (1200 MHz)** mit $50\ \Omega$-Referenztoren.

Das Projekt wurde in **Cadence AWR Microwave Office (MWO)** entwickelt und beinhaltet:
1. Ideale Schaltung mit konzentrierten Elementen (L-Glieder).
2. Streifenleitungsmodell unter Berücksichtigung von Substratparametern, Vorspannungsnetzwerken und gemessenen SMA-Steckverbinderübergängen.
3. 2D-Leiterplatten-Layout (PCB Layout).
4. Smith-Diagramme, Reflexionsfaktor ($S_{11}$), Vorwärtsübertragungsgewinn ($S_{21}$) und Stehwellenverhältnis (VSWR).

---

## Projektstruktur

```text
├── cad/                                        # 3D-CAD-Modelle und PCB-Layoutdateien
│   └── .gitkeep
├── calculations/                               # Mathematische Modelle (Mathcad, SMath)
│   └── .gitkeep
├── docs/
│   └── images/                                 # Dokumentationsgrafiken, Schaltpläne und Simulationsergebnisse
│       ├── layout_pcb_amplifier_2sk2974.png    # 2D-Leiterplattenlayout des Verstärkers in AWR MWO
│       ├── plot_microstrip_s21_gain.png        # Vorwärtsübertragungsgewinn |S21| der Streifenleitungsschaltung
│       ├── plot_microstrip_vswr.png            # Stehwellenverhältnis (VSWR) der Tore 1 und 2
│       ├── plot_s11_frequency_response.png     # Frequenzgang des Reflexionsfaktors |S11| (ideale Schaltung)
│       ├── plot_s21_frequency_response.png     # Frequenzgang des Übertragungsgewinns |S21| (ideale Schaltung)
│       ├── plot_vswr_frequency_response.png    # Stehwellenverhältnis (VSWR) der idealen Schaltung
│       ├── schematic_2sk2974_matching_1200mhz.png # Schaltplan der idealen Anpassungsschaltung
│       ├── schematic_microstrip_pa_2sk2974.png # Streifenleitungsschaltplan mit Bias-Netzwerk und SMA
│       ├── smith_chart_microstrip_s11_s22.png  # Smith-Diagramm S11 & S22 der Streifenleitungsschaltung
│       ├── smith_chart_s11_matching.png        # Smith-Diagramm für S11 (ideale Schaltung)
│       └── smith_chart_s22_matching.png        # Smith-Diagramm für S22 (ideale Schaltung)
├── simulation/                                 # Cadence AWR Microwave Office Projektdateien
│   ├── amplifier_2sk2974_1200mhz_microstrip.emp # Projekt mit Streifenleitungsschaltung, SMA und Layout
│   └── transistor_2sk2974_matching_1200mhz.emp  # Projekt mit idealen konzentrierten Elementen
├── .gitignore                                  # Ausschlussregeln für EDA- und Betriebssystemdateien
├── LICENSE                                     # Vollständiger Lizenztext der CERN-OHL-P v2
├── README.de.md                                # Dokumentation in deutscher Sprache
├── README.en.md                                # Dokumentation in englischer Sprache
└── README.md                                   # Dokumentation in russischer Sprache
```

---

## Transistorparameter

Das aktive Bauelement ist als Zweitor modelliert (`S2P_BLK ID=X1`). Bei der Betriebsfrequenz $f_0 = 1,2\text{ GHz}$ lauten die Streuparameter bezogen auf $Z_0 = 50\ \Omega$:

$$\begin{aligned}
S_{11} &= 0,96198 \angle 178,34^\circ \\
S_{21} &= 0,19253 \angle 30,1079^\circ \\
S_{12} &= 0,03660 \angle 80,3997^\circ \\
S_{22} &= 0,93364 \angle -178,72^\circ
\end{aligned}$$

---

## Schaltungsaufbau und PCB-Layout

### 1. Ideale Schaltung mit konzentrierten Elementen

<p align="center">
  <img src="docs/images/schematic_2sk2974_matching_1200mhz.png" alt="Schaltplan 2SK2974 Anpassung" width="850"/>
  <br>
  <em>Abbildung 1 — Prinzipschaltbild des idealen 2SK2974-Anpassungsnetzwerks bei 1,2 GHz</em>
</p>

- **Tore**: `PORT P=1` ($Z = 50\ \Omega$) und `PORT P=2` ($Z = 50\ \Omega$).
- **Eingangsanpassungsglied**: Parallelkondensator `C1 = 21,8 pF` und Serieninduktivität `L1 = 0,862 nH`.
- **Ausgangsanpassungsglied**: Serieninduktivität `L2 = 1,4 nH` und Parallelkondensator `C2 = 16,4 pF`.

### 2. Streifenleitungsschaltung mit Bias-Netzwerken und SMA-Verbindern

<p align="center">
  <img src="docs/images/schematic_microstrip_pa_2sk2974.png" alt="Streifenleitungsschaltplan 2SK2974" width="850"/>
  <br>
  <em>Abbildung 2 — Streifenleitungsschaltplan des Verstärkers mit Vorspannungs-Stubs und gemessenen SMA-Subschaltungen</em>
</p>

- **Substratparameter (`MSUB ID=SUB1`)**:
  - Relative Dielektrizitätskonstante: $\varepsilon_r = 4,5$ (FR-4)
  - Substratdicke: $H = 1,0\text{ mm}$
  - Kupferkaschierung: $T = 0,035\text{ mm}$ ($35\ \mu\text{m}$)
  - Verlustfaktor: $\tan\delta = 0,015$
  - Spezifischer Widerstand: $\rho = 0,0172$
- **HF-Verbinder**: Subschaltungen `SUBCKT NET="SMA_Measured_Thru"` basierend auf gemessenen S-Parametern von End-Launch-SMA-Verbindern.
- **Gleichspannungs-Trennkapazitäten**: `C2 = 10000 pF` ($10\text{ nF}$) und `C3 = 10000 pF` ($10\text{ nF}$) zur Gleichstromentkopplung.
- **Gate- und Drain-Vorspannungsnetzwerke**: Lambda-Viertel-Streifenleitungsstubs `MLIN` ($W = 17,577\text{ mm}$, $L = 34,603\text{ mm}$) mit HF-Abblockkondensatoren `C5, C6 = 10000 pF` gegen Masse.
- **Mikrostreifen-T-Verzweigungen**: `MTEE`-Elemente mit Armbreiten $W_1 = W_2 = W_3 = 1\text{ mm}$.

### 3. Leiterplattenlayout (PCB Layout)

<p align="center">
  <img src="docs/images/layout_pcb_amplifier_2sk2974.png" alt="Leiterplattenlayout Verstärker 2SK2974" width="850"/>
  <br>
  <em>Abbildung 3 — 2D-Streifenleitungs-Layout in Cadence AWR Microwave Office</em>
</p>

Das Layout integriert die Montagefläche des Leistungstransistors 2SK2974 mit Bohrungen zur Befestigung am Kühlkörper, breite Vorspannungspolygone, SMD-Pads und Anschlussflächen für End-Launch-SMA-Buchsen.

---

## Simulationsergebnisse

### 1. Ergebnisse der idealen Schaltung

<p align="center">
  <img src="docs/images/smith_chart_s11_matching.png" alt="S11 Smith-Diagramm" width="48%"/>
  <img src="docs/images/smith_chart_s22_matching.png" alt="S22 Smith-Diagramm" width="48%"/>
  <br>
  <em>Abbildung 4 — Smith-Diagramme der idealen Schaltung: S11 (links) und S22 (rechts)</em>
</p>

<p align="center">
  <img src="docs/images/plot_s11_frequency_response.png" alt="S11 Frequenzgang" width="48%"/>
  <img src="docs/images/plot_s21_frequency_response.png" alt="S21 Frequenzgang" width="48%"/>
  <br>
  <em>Abbildung 5 — Frequenzgang von |S11| (links) und Übertragungsgewinn |S21| (rechts)</em>
</p>

<p align="center">
  <img src="docs/images/plot_vswr_frequency_response.png" alt="VSWR Frequenzgang" width="700"/>
  <br>
  <em>Abbildung 6 — Stehwellenverhältnis (VSWR) am Eingang der idealen Schaltung</em>
</p>

- Eingangsreflexionsfaktor bei $1200\text{ MHz}$: $|S_{11}| = -18,32\text{ dB}$.
- Vorwärtsübertragungsfaktor (Stufenverstärkung): $|S_{21}| = +6,141\text{ dB}$.
- Resonanz-Stehwellenverhältnis: $\text{VSWR} \approx 1,27 - 1,36$ ($1,362$ bei $1204\text{ MHz}$).

### 2. Ergebnisse der Streifenleitungsschaltung (mit Bias und SMA)

<p align="center">
  <img src="docs/images/smith_chart_microstrip_s11_s22.png" alt="Smith-Diagramm Streifenleitung" width="600"/>
  <br>
  <em>Abbildung 7 — Smith-Diagramm für die Streifenleitungsschaltung: S11 (Dreieck) und S22 (Quadrat) bei 1,2 GHz</em>
</p>

<p align="center">
  <img src="docs/images/plot_microstrip_vswr.png" alt="VSWR Streifenleitung" width="48%"/>
  <img src="docs/images/plot_microstrip_s21_gain.png" alt="Verstärkung Streifenleitung" width="48%"/>
  <br>
  <em>Abbildung 8 — Streifenleitungsverstärker bei 1,2 GHz: VSWR der Tore 1 und 2 (links) und Vorwärtsübertragungsgewinn |S21| (rechts)</em>
</p>

- **Impedanzanpassung im Smith-Diagramm**: $S(1,1)$ und $S(2,2)$ bei $1,2\text{ GHz}$ liegen im Zentrum ($50\ \Omega$).
- **Stehwellenverhältnis (VSWR)**:
  - $\text{VSWR}_1$ (Tor 1): **$1,442$**
  - $\text{VSWR}_2$ (Tor 2): **$1,308$**
- **Vorwärtsübertragungsgewinn**: Unter Berücksichtigung der physikalischen Verluste im FR-4-Dielektrikum ($\tan\delta = 0,015$), ohmscher Leitungsverluste und SMA-Übergangsdiskontinuitäten beträgt der Übertragungsgewinn $|S_{21}|$ bei $1,2\text{ GHz}$ **$+4,710\text{ dB}$** ($4,7097\text{ dB}$).

---

## Lizenz

Copyright (c) 2026 Ilya Kornilov

Diese Quelle beschreibt Open Hardware (offene Hardware) und ist unter der CERN-OHL-P v2 lizenziert. 
Sie dürfen diese Quelle unter den Bedingungen der CERN-OHL-P v2 (https://cern.ch/cern-ohl) 
weiterverbreiten, modifizieren und Produkte auf deren Grundlage herstellen.

Diese Quelle wird OHNE JEGLICHE AUSDRÜCKLICHE ODER STILLSCHWEIGENDE GEWÄHRLEISTUNG vertrieben, 
EINSCHLIESSLICH DER GEWÄHRLEISTUNG DER MARKTGÄNGIGKEIT, ZUFRIEDENSTELLENDEN QUALITÄT ODER EIGNUNG 
FÜR EINEN BESTIMMTEN ZWECK. Die geltenden Bedingungen entnehmen Sie bitte der CERN-OHL-P v2.
