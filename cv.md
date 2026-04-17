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

- **System Architecture:** Designed an 8-channel neural stimulation ASIC supporting 1:8 time-division multiplexing (TDM) to drive 64 electrodes, with SPI-based channel configuration for wirelessly powered cortical visual prosthesis applications
- **Low-Power Hierarchical Biasing Architecture:** Implemented a global bias block distributing gate voltages to per-channel current mirrors, achieving approximately 20 µW static power consumption per channel
- **PI Closed-Loop Controller:** Replaced the original open-loop amplifier architecture to eliminate steady-state error and improve system stability during load switching
- **Active-Feedback IDAC:** Isolated high voltage via op-amp output clamping; employed 2 V devices in the second-stage cascode to improve speed, reducing headroom from 250 mV to 60 mV (76% reduction)
- **Idle Controller:** Detected excess voltage headroom and automatically disabled the PI controller and comparator to optimize efficiency during high-to-low load transitions
- **CMLS-Based Level Shifter:** Resolved insufficient rising-edge speed in the original cross-coupled architecture, optimizing rectifier switch control signals from glitch-prone waveforms to clean square waves
- **Complementary NMOS Rectifier Switch:** Automatically switched to NMOS conduction under low-voltage conditions, achieving a 150% efficiency improvement at 5 µA / 20 kΩ load
- **VCDL Optimization:** Optimized delay tuning range from 33 ns–800 ps to 10 ns–170 ps (70% reduction in minimum delay), enabling fast TDM switching
- **Performance:** Output current 0–155 µA (5 µA step), load resistance 20–70 kΩ, support for 1–20 nF capacitive loads; overall efficiency superior to the original system and operational down to 5 µA; robustness verified via Monte Carlo and four process-corner simulations
- **Current Status:** Circuit design and core layout completed; tape-out scheduled for April 20, 2026

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