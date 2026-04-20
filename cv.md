# Xiaoyun Qian

**Major:** Microelectronics  
**Phone:** +86 13816092395 | +31 682320671  
**Email:** qianxiaoyun2000@outlook.com

---

## Education

**Delft University of Technology** (September 2023 – Present)  
M.Sc. Microelectronics  
Thesis: *Design of an Energy-Efficient Multichannel Neural Stimulation System for Wireless Visual Cortex Stimulation*  
Expected: July 2026

**HZ University of Applied Sciences, the Netherlands** (February 2022 – July 2023)  
Bachelor of Engineering (HBO)  
Thesis: *Design and Manufacturing of a Battery Pack Clamping System with Integrated Vision Positioning for Laser Welding*

**Shanghai Maritime University** (September 2019 – July 2023)  
Bachelor of Engineering — Mechatronics Engineering

---

## Work Experience

**Shanghai United Imaging Healthcare Co., Ltd.** — Intern  
July – August 2025 | Shanghai, China

- Participated in PET medical imaging sensor development in the MI-DET-DEA department, contributing to the "Kunpeng" and "Qilin" projects
- Conducted electromagnetic shielding performance testing and failure analysis of sensors, identified EMI interference sources, and proposed improvement solutions to enhance signal integrity
- Executed vibration testing for "Kunpeng" and environmental stress testing for "Qilin" (Kunshan laboratory), participated in failure analysis and assisted design optimization to verify medical-grade reliability

**EmergoStar B.V.** — Electrical Engineer  
January – August 2023 | Terneuzen, the Netherlands

- Designed a battery pack clamping system capable of withstanding 3000 N clamping force, optimized the structure through mechanical simulation to improve battery lifespan
- Fabricated test tools using 3D printing and integrated a vision positioning system into a laser welding platform, enabling precise positioning and modular improvements
- Developed a pressure sensor testing platform using Arduino, enabling real-time monitoring of clamping force distribution, validating system performance, and reducing assembly changeover time by over 30%

**Lumileds (Shanghai) Management Co., Ltd.** — Intern  
July – August 2021 | Shanghai, China

- Performed high-precision mounting of semiconductor light source chips using microscopes and thermocompression soldering tools, gaining experience in precision soldering processes
- Conducted driving tests using constant-current power supplies and fabricated engineering samples for customer demonstrations of LED performance
- Maintained and updated the company's internal product data server, providing update logs and user documentation

---

## Projects

**Design of an Energy-Efficient Multichannel Neural Stimulation System for Wireless Visual Cortex Stimulation** (September 2025 – July 2026, Ongoing) — MSc Thesis, TU Delft

- **Process & Chip:** TSMC 180nm BCD; 8-channel ASIC in 2 × 2.5 mm die; tape-out April 2026; iterated on Varkevisser et al. (TCAS-I 2025) with 8 key architectural improvements
- **System Architecture:** 8-channel neural stimulation ASIC with 1:8 TDM driving 64 electrodes; SPI-based per-channel configuration (current amplitude, electrode select, polarity) for wirelessly powered cortical visual prosthesis
- **PI Closed-Loop Controller:** Replaced open-loop OTA with PI controller, eliminating steady-state headroom error; V_headroom regulated to 75 mV at 13.56 MHz sampling rate with ≥45° phase margin across all corners
- **Active-Feedback IDAC:** Op-amp output clamping isolates high-voltage compliance; 2 V cascode devices in second stage reduce current sink headroom from 250 mV to 60 mV (76% reduction), directly improving power efficiency
- **Complementary NMOS Rectifier Switch:** Auto-switches to NMOS conduction path under low-voltage conditions, achieving 150% efficiency improvement at 5 µA / 20 kΩ load
- **Idle Controller:** Detects excess headroom (>200 mV) and disables comparator + PI controller during high-to-low load transitions, eliminating unnecessary switching losses
- **CMLS-Based Level Shifter:** Resolved rising-edge speed limitation of cross-coupled architecture; rectifier switch control signals optimized from glitch-prone to clean square waves
- **VCDL Optimization:** Tuning range narrowed from 33 ns–800 ps to 10 ns–170 ps (70% reduction in minimum delay), enabling fast TDM channel switching
- **Low-Power Hierarchical Biasing:** Global bias block distributes gate voltages to per-channel current mirrors; per-channel local bias (V_REF 60 mV, V_REF_OUT 75 mV) prevents inter-channel crosstalk; ~20 µW static power per channel
- **Layout:** Single-channel floorplan ~390 × 376 µm (~0.15 mm²); 8 channels total ~1.18 mm² (24% of die); Co (MOS+MIM stacked, 80 pF) is area-dominant block at 250 × 135 µm
- **Performance:** Output current 0–155 µA (5 µA step, doubled vs. reference design), load 20–70 kΩ, capacitive loads 1–20 nF; robustness verified via Monte Carlo and four process-corner simulations

**BiCMOS Process Fabrication and Device Characterization Laboratory** (2025)

- Completed a full BiCMOS fabrication flow including thermal oxidation, ion implantation, lithography, wet and dry etching, and metallization
- Performed process and device simulations using Synopsys Sentaurus to predict doping profiles and electrical characteristics
- Characterized NMOS/PMOS devices using a Keysight B1500A semiconductor parameter analyzer, extracting threshold voltage, mobility, and subthreshold swing
- Conducted sheet resistance measurements, ELM measurements, and lateral diffusion analysis to validate simulation model accuracy

**High-Precision Residue Amplifier Design** (2024–2025)

- Designed a two-stage fully differential amplifier achieving 66 dB SNR, 80.33 dB gain, and 24.88 ns settling time
- Developed Python scripts to automate LTspice simulations, enabling parameter extraction, design space exploration, and iterative optimization
- Applied Miller compensation to optimize frequency response; reduced power consumption to 0.828 mW through noise–bandwidth trade-offs, achieving a FoM of −173.8 dB
- Designed complete common-mode feedback and bias networks; completed transient analysis, noise analysis, and performance verification

**Kaggle Titanic – Machine Learning from Disaster** (2023)

- Built deep learning models using PyTorch to predict Titanic passenger survival, including feature engineering and data preprocessing
- Compared multiple machine learning algorithms and optimized hyperparameters to improve model performance
- Achieved final ranking of 559 / 14,393 (Top 4%) with an accuracy of 0.799

---

## Skills

- **Languages:** English (CET-4, CET-6), Japanese (JLPT N4), Dutch (A1)
- **Programming:** Python, PyTorch, C++, LaTeX
- **EDA & Simulation:** Cadence Virtuoso, LTspice, HSPICE, Synopsys Sentaurus
- **Other Tools:** MATLAB, Jupyter Notebook, SolidWorks, Arduino
- **Process Experience:** TSMC 180nm BCD (thesis tape-out); BiCMOS full fabrication flow (lab)