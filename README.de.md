<p align="center">
  <a href="README.md">Русский</a> | <a href="README.en.md">English</a> | <b>Deutsch</b>
</p>

# 2sk2974-1g2-match: Komplexe Impedanzanpassung des UHF-MOSFET-Viertors 2SK2974 bei 1,2 GHz

Dieses Repository umfasst den Entwurf, die analytische Synthese und die numerische Schaltungssimulation eines reaktiven L-Glied-Anpassungsnetzwerks mit konzentrierten Elementen für den UHF-Leistungs-MOSFET **2SK2974** bei einer Betriebsfrequenz von **1,2 GHz (1200 MHz)**. Das Netzwerk transformiert die niederohmigen komplexen Gate- und Drain-Impedanzen an standardisierte $50\ \Omega$-Referenztore.

Das Projekt beinhaltet analytische Berechnungen über Kettenmatrizen (ABCD), eine Schaltungssimulation in **Cadence AWR Microwave Office (MWO)**, Smith-Diagramme sowie Frequenzgänge des Reflexionsfaktors ($S_{11}$) und des Stehwellenverhältnisses (VSWR).

---

## Projektstruktur

```text
├── cad/                                        # 3D-CAD-Modelle und PCB-Layoutdateien
│   └── .gitkeep
├── calculations/                               # Mathematische Modelle (Mathcad, SMath, Analytik)
│   └── .gitkeep
├── docs/
│   └── images/                                 # Dokumentationsgrafiken, Schaltpläne und Simulationsergebnisse
│       ├── plot_s11_frequency_response.png     # Frequenzgang des Eingangsreflexionsfaktors |S11| (dB)
│       ├── plot_vswr_frequency_response.png    # Frequenzgang des Stehwellenverhältnisses (VSWR)
│       ├── schematic_2sk2974_matching_1200mhz.png # Schaltplan in Cadence AWR Microwave Office
│       ├── smith_chart_s11_matching.png        # Smith-Diagramm für S11
│       └── smith_chart_s22_matching.png        # Smith-Diagramm für S22
├── simulation/                                 # Numerische Simulationsdateien
│   └── transistor_2sk2974_matching_1200mhz.emp # Cadence AWR Microwave Office Projektdatei
├── .gitignore                                  # Ausschlussregeln für EDA- und Betriebssystemdateien
├── LICENSE                                     # Vollständiger Lizenztext der CERN-OHL-P v2
├── README.de.md                                # Dokumentation in deutscher Sprache
├── README.en.md                                # Dokumentation in englischer Sprache
└── README.md                                   # Dokumentation in russischer Sprache
```

---

## Analytische Berechnung

Der aktive Transistor wird als lineares Viertor (Zweitor) modelliert, das bei $f_0 = 1,2\text{ GHz}$ durch folgende Streuparameter bezogen auf ein Bezugssystem von $Z_0 = 50\ \Omega$ spezifiziert ist:

$$\begin{aligned}
S_{11} &= 0,96198 \angle 178,34^\circ \\
S_{21} &= 0,19253 \angle 30,1079^\circ \\
S_{12} &= 0,03660 \angle 80,3997^\circ \\
S_{22} &= 0,93364 \angle -178,72^\circ
\end{aligned}$$

### Ermittlung der charakteristischen Torimpedanzen

Die intrinsischen Torimpedanzen des Transistors werden mittels bilinearer Transformation berechnet:

$$Z_{in} = Z_0 \frac{1 + S_{11}}{1 - S_{11}} \approx 0,9691 + j0,7241\ \Omega$$

$$Z_{out} = Z_0 \frac{1 + S_{22}}{1 - S_{22}} \approx 1,7161 - j0,5579\ \Omega$$

Die Werte weisen sehr geringe Realteile ($R_{in} < 1\ \Omega$, $R_{out} < 2\ \Omega$) auf, was für HF-Leistungs-MOSFETs im UHF-Bereich charakteristisch ist. Die Aufgabe des Netzwerks besteht in der reflexionsarmen Transformation auf $50\ \Omega$.

### Synthese der reaktiven L-Anpassungsglieder

Aufgrund der Bedingung $R_0 = 50\ \Omega > R_{in}, R_{out}$ werden Parallelkomponenten an den $50\ \Omega$-Toren und Serienkomponenten an den niederohmigen Transistoranschlüssen angeordnet:
1. **Eingangsnetzwerk (Tor 1 $\rightarrow$ Gate)**:
   - Parallele Kapazität $C_1 = 21,8\text{ pF}$ am $50\ \Omega$-Quelltor zur Transformation des Leitwerts.
   - Serieninduktivität $L_1 = 0,862\text{ nH}$ in Reihe zum Gate zur Kompensation der verbleibenden Suszeptanz.
2. **Ausgangsnetzwerk (Drain $\rightarrow$ Tor 2)**:
   - Serieninduktivität $L_2 = 1,400\text{ nH}$ unmittelbar am Drain-Anschluss.
   - Parallele Kapazität $C_2 = 16,4\text{ pF}$ am $50\ \Omega$-Lasttor.

### Kaskadenanalyse mittels ABCD-Kettenmatrizen

Die Gesamtschaltung wird analytisch über Übertragungsmatrizen modelliert:

$$[M_{gesamt}] = \begin{bmatrix} 1 & 0 \\ j\omega C_1 & 1 \end{bmatrix} \begin{bmatrix} 1 & j\omega L_1 \\ 0 & 1 \end{bmatrix} [M_{trans}] \begin{bmatrix} 1 & j\omega L_2 \\ 0 & 1 \end{bmatrix} \begin{bmatrix} 1 & 0 \\ j\omega C_2 & 1 \end{bmatrix}$$

Ergebnisse der theoretischen Berechnung bei $1,2\text{ GHz}$:
- Eingangsreflexionsfaktor: $|S_{11}| = 0,1205$ ($-18,38\text{ dB}$)
- Ausgangsreflexionsfaktor: $|S_{22}| = 0,1211$ ($-18,34\text{ dB}$)
- Vorwärtsübertragungsgewinn: $|S_{21}| = 2,0278$ ($+6,14\text{ dB}$)
- Stehwellenverhältnis: $\text{VSWR}_1 = 1,274$, $\text{VSWR}_2 = 1,276$

---

## Schaltungsentwurf

Die numerische Schaltungssimulation wurde in Cadence AWR Microwave Office aufgebaut.

<p align="center">
  <img src="docs/images/schematic_2sk2974_matching_1200mhz.png" alt="Schaltplan 2SK2974 Anpassung" width="850"/>
  <br>
  <em>Abbildung 1 — Prinzipschaltbild des 2SK2974-Anpassungsnetzwerks bei 1,2 GHz (Cadence AWR MWO)</em>
</p>

### Beschreibung der Komponenten
- **HF-Abschlüsse**: Unsymmetrische $50\ \Omega$-Tore `PORT P=1` und `PORT P=2`.
- **Transistormodell**: Zweitor-Streuparameter-Block `S2P_BLK ID=X1` mit den Messdaten des 2SK2974.
- **Eingangszweig**: Parallelkondensator `CAP ID=C1` ($C = 21,8\text{ pF}$) gegen Masse und Serieninduktivität `IND ID=L1` ($L = 0,862\text{ nH}$).
- **Ausgangszweig**: Serieninduktivität `IND ID=L2` ($L = 1,4\text{ nH}$) und Parallelkondensator `CAP ID=C2` ($C = 16,4\text{ pF}$) gegen Masse.

---

## Simulationsergebnisse

Die Simulation wurde im Frequenzbereich von $0$ bis $2000\text{ MHz}$ durchgeführt.

### Smith-Diagramme ($S_{11}$ und $S_{22}$)

Die normierten Reflexionsfaktoren sind in den folgenden Smith-Diagrammen dargestellt.

<p align="center">
  <img src="docs/images/smith_chart_s11_matching.png" alt="S11 Smith-Diagramm" width="48%"/>
  <img src="docs/images/smith_chart_s22_matching.png" alt="S22 Smith-Diagramm" width="48%"/>
  <br>
  <em>Abbildung 2 — Impedanzortskurven für Tor 1 (links) und Tor 2 (rechts) im Smith-Diagramm</em>
</p>

Bei der Zielbetriebsfrequenz von $1200\text{ MHz}$ treffen die Ortskurven beider Tore präzise den Koordinatenursprung des Diagramms ($z \approx 1,0 + j0,0$), was eine vollständige Kompensation der Blindanteile und Transformation der Wirkanteile belegt.

### Frequenzgang der Eingangsreflexion ($S_{11}$)

<p align="center">
  <img src="docs/images/plot_s11_frequency_response.png" alt="S11 Frequenzgang" width="750"/>
  <br>
  <em>Abbildung 3 — Betrag des Eingangsreflexionsfaktors |S11| (dB) über der Frequenz</em>
</p>

- Bei $1200\text{ MHz}$ zeigt die Kennlinie eine scharfe Resonanzsenke mit **$-18,32\text{ dB}$** (reflektierte Leistung $< 1,5\%$).
- Die Anpassbandbreite für das Kriterium $|S_{11}| \le -10\text{ dB}$ erstreckt sich von $1130\text{ MHz}$ bis $1240\text{ MHz}$ ($\Delta f \approx 110\text{ MHz}$, relative Bandbreite $\approx 9,2\%$).

### Stehwellenverhältnis (VSWR)

<p align="center">
  <img src="docs/images/plot_vswr_frequency_response.png" alt="VSWR Frequenzgang" width="750"/>
  <br>
  <em>Abbildung 4 — Frequenzabhängigkeit des Stehwellenverhältnisses (VSWR) am Eingang</em>
</p>

- Bei Resonanz liegt das Stehwellenverhältnis im Bereich von **$1,27 - 1,36$** (der Marker bei $1204\text{ MHz}$ zeigt einen Wert von $\text{VSWR} = 1,362$).
- Das Kriterium eines zulässigen Stehwellenverhältnisses ($\text{VSWR} \le 2,0$) wird im gesamten Nutzfrequenzkanal um $1,2\text{ GHz}$ zuverlässig eingehalten.

### Übereinstimmung von Modell und Berechnung

| Parameter | Analytische Berechnung (ABCD) | Numerische Simulation (AWR MWO) | Abweichung |
| :--- | :---: | :---: | :---: |
| Resonanzfrequenz $f_0$ | $1200\text{ MHz}$ | $1200\text{ MHz}$ | $0,0\%$ |
| Eingangsreflexion $\|S_{11}\|$ | $-18,38\text{ dB}$ | $-18,32\text{ dB}$ | $0,06\text{ dB}$ |
| Ausgangsreflexion $\|S_{22}\|$ | $-18,34\text{ dB}$ | $-18,30\text{ dB}$ | $0,04\text{ dB}$ |
| Stehwellenverhältnis Eingang ($\text{VSWR}_1$) | $1,274$ | $1,28 - 1,36$ | innerhalb der Toleranz |

Die numerischen Daten aus Cadence AWR Microwave Office weisen eine sehr hohe Übereinstimmung mit dem analytischen Modell auf.

---

## Lizenz

Copyright (c) 2026 Ilya Kornilov

Diese Quelle beschreibt Open Hardware und ist unter der CERN-OHL-P v2 lizenziert. 
Sie dürfen diese Quelle gemäß den Bedingungen der CERN-OHL-P v2 (https://cern.ch/cern-ohl) 
weitergeben, modifizieren und Produkte unter Verwendung dieser Quelle herstellen.

Diese Quelle wird OHNE JEDE AUSDRÜCKLICHE ODER STILLSCHWEIGENDE GEWÄHRLEISTUNG 
BEREITGESTELLT, EINSCHLIESSLICH DER MARKTGÄNGIGKEIT, ZUFRIEDENSTELLENDEN QUALITÄT 
UND EIGNUNG FÜR EINEN BESTIMMTEN ZWECK. Bitte beachten Sie die CERN-OHL-P v2 
für die geltenden Bedingungen.
