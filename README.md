# Design and Analysis of a High-Efficiency GaN RF Power Amplifier

## Project Description
This project focuses on the design, optimization, and characterization of a high-efficiency RF Power Amplifier (PA) utilizing Gallium Nitride (GaN) High Electron Mobility Transistor (HEMT) technology. GaN devices are the industry standard for high-power, high-frequency applications—such as 5G base stations, satellite communications, and radar systems—due to their wide bandgap, superior power density, and high breakdown voltage limits compared to conventional Silicon or GaAs alternatives.

The primary objective of this work is to achieve optimal output power ($P_{out}$), high Power-Added Efficiency (PAE), and stable power gain over the targeted RF operating band while ensuring absolute circuit stability across all driving conditions.

---

## Technical Specifications & Targets
* **Transistor Technology:** Wolfspeed CGH40010F GaN HEMT PDK
* **Center Frequency:** 2.4 GHz
* **Operating Class:** High-Efficiency Class AB
* **Nominal Drain Bias ($V_{ds}$):** 28 V
* **Quiescent Current ($I_{dq}$):** ~200 mA

---

## Repository Directory Structure

The internal directories are structured cleanly to showcase individual design modules:

📦 GaN-Power-Amplifier
 ┣ 📂 design_files
 ┃ ┣ 📜 schematic_setups.ads       # Keysight ADS design workspace files
 ┃ ┗ 📜 cgh40010f_model.pdk        # Device foundry PDK integration file
 ┣ 📂 test_benches
 ┃ ┣ 📜 load_pull_workspace        # Impedance tuner workspace tracking
 ┃ ┗ 📜 harmonic_balance_bench     # Saturation sweep setup scripts
 ┣ 📂 plots
 ┃ ┣ 📜 dc_iv_characteristics.png  # Transistor current-voltage sweep curves
 ┃ ┣ 📜 stability_k_factor.png     # Rollett stability metric graph across spectrum
 ┃ ┣ 📜 load_pull_contours.png     # Power and PAE contours mapped on Smith Chart
 ┃ ┗ 📜 hb_saturation_curves.png   # Large-signal PAE, Gain, and P1dB curves
 ┗ 📜 README.md                    # System characterization and documentation

---

## Engineering Design & Simulation Pipeline

The power amplifier design was executed through a rigorous, sequential workflow within Keysight Advanced Design System (ADS):

### 1. Model Setup and DC Biasing (IV Curves)
* **Action:** Imported the Wolfspeed CGH40010F PDK into ADS and configured a DC simulation schematic (`DC_Simulation`).
* **Methodology:** Swept the gate voltage ($V_{gs}$ from $-4\text{ V}$ to $0\text{ V}$) and drain voltage ($V_{ds}$ from $0\text{ V}$ to $40\text{ V}$) to trace the complete device operational output characteristics.
* **Analysis:** Located the pinch-off boundary at approximately $-3\text{ V}$. Established a stable Class AB quiescent bias point at $V_{ds} = 28\text{ V}$ with an $I_{dq}$ target of ~200 mA to secure the optimum compromise between linearity and structural efficiency.

### 2. Bias Networks (Feed Circuits)
* **Action:** Formulated decoupled bias distribution networks to feed DC power to the gate and drain terminals while guaranteeing strict RF isolation.
* **Implementation:** Integrated quarter-wavelength ($\lambda/4$) microstrip transmission lines terminated with localized RF bypass capacitors. At the 2.4 GHz center frequency, this network emulates an ideal open circuit (RF Choke), preventing high-frequency leakage back into the DC power rails.

### 3. Stability Analysis
* **Action:** Ran small-signal linear AC simulations to evaluate the Rollett stability factor ($K$) and Mu ($\mu$) parameters from 100 MHz out to 10 GHz.
* **Analysis:** Monitored potential out-of-band oscillation points ($K < 1$).
* **Compensation:** Added a parallel RC network and series resistance directly into the gate feeding path to damp out destabilizing low-frequency gain, successfully maintaining unconditional stability ($K > 1$) across the entire swept frequency spectrum.

### 4. Load-Pull Simulation (Large-Signal Core)
* **Action:** Executed large-signal Load-Pull simulations to optimize output power matching, bypassing small-signal $S_{22}$ limitations under non-linear compression.
* **Mechanism:** Configured an automated impedance tuner model at the CGH40010F drain port to mathematically map hundreds of load impedance coordinates.
* **Analysis:** Plotted constant $P_{out}$ and PAE performance contours directly onto the Smith Chart. Extracted the optimum compromise coordinate to serve as the targeted target load impedance ($Z_{L,opt}$).

### 5. Impedance Matching Network (IMN & OMN) Design
* **Action:** Leveraged the ADS Smith Chart Utility toolset to calculate and layout physical microstrip matching lines.
* **Output Matching Network (OMN):** Transformed the conventional $50\ \Omega$ antenna system load termination directly to the complex $Z_{L,opt}$ mapped via the load-pull data.
* **Input Matching Network (IMN):** Transformed the incoming $50\ \Omega$ generator path to the complex conjugate of the transistor's input impedance ($Z_{in}^*$), maximizing localized power delivery.

### 6. Harmonic Balance (HB) Verification
* **Action:** Ran large-signal Harmonic Balance simulations, sweeping the input power ($P_{in}$) up into deep saturation ($+35\text{ dBm}$).
* **Verified Performance Metrics:**
  * **Power-Added Efficiency (PAE):** Verified peak efficiency targets ($>60\%$) expected of high-performance Class AB operation.
  * **Power Gain:** Profiled gain flatness constraints across the linear region before compression sets in.
  * **Compression Point Extraction:** Evaluated the 1-dB ($P_{1dB}$) and 3-dB ($P_{3dB}$) compression points to define clear power handling boundaries.

---

## Verification Environment Setup
1. Launch **Keysight ADS** and restore the archived `.7zap` workspace file from the `design_files/` directory.
2. Ensure the **Wolfspeed CGH40010F PDK** path variables are properly pointed to your local design kit index.
3. Open individual simulation cells corresponding to the numbered milestones above (`01_DC_Bias`, `03_Stability`, `04_LoadPull`, `06_HarmonicBalance`) to view data displays and trace performance curves.

---

## Contributors
* [Shashikant](https://github.com/Shashi-kalal)
* [Tejaswini K N](https://github.com/tejaswini1009)
