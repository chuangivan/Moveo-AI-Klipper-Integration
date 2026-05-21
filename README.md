# Moveo-AI: Rebirthing the Giant 3D Printed Robot Arm with Klipper & LLM

🚀 **Project Vision**
In 201x, I built the BCN3D Moveo (Shared on Instructables’ “Build a Giant 3D Printed Robot Arm” as “Ivan Chuang made it!”). Now, in 2026, it's time for a massive brain transplant. This project documents the complete evolution from a legacy 8-bit Arduino/RAMPS setup to a high-performance, 32-bit Klipper-based system integrated with Large Language Models (LLMs), Voice Recognition (Whisper), and Microphone-Array-based DOA (Direction of Arrival).

---

## 🛠 Phase 1: Hardware Core & Power System (Current Stage)

The goal of this phase is to establish a rock-solid electrical and computing foundation capable of high-torque, silent, and precise 6-axis synchronous motion. 

### ⚡ Key Hardware Upgrades:
* **Mainboard (MCU):** Replaced Arduino Mega 2560 + RAMPS 1.4 with the powerhouse **FYSETC Spider V3.0 (STM32H723)**, offering microsecond-level pulse synchronization and ample hardware interfaces.
* **Primary Power Supply:** Upgraded to a single **Mean Well 36V Supply**, eliminating unnecessary buck converters for motor drivers and achieving a unified high-voltage power bus.
* **Hybrid Driver Architecture:**
    * **High-Load Joints (Axis 1~2: Base & Shoulder dual NEMA 23):** Driven by external industrial-grade **STEPPERONLINE DM556Y** digital drivers. Configured in a Common Anode setup with a direct 5V signal link (no external current-limiting resistors required).
    * **Precision/Silent Joints (Axis 3~6: Elbow & Wrist 3-Axis NEMA 17):** Driven by **4x onboard TMC2240 drivers** plugged directly into the Spider V3.0 via **SPI bus communication**. 

### 💡 Why 36V & TMC2240?
* **Torque & Speed:** Higher voltage allows the stepper motors to maintain their torque curves at much higher angular velocities, preventing step-skipping during rapid multi-axis interpolations.
* **Industrial Elegance:** TMC2240 supports a wide input voltage up to 60V, allowing the onboard drivers to share the 36V main power bus directly. This removes the risk of motor regenerative voltage spikes burning out cheap buck converters.
* **Acoustic & Intelligence:** With **StealthChop2** and **StallGuard 4**, the critical elbow and wrist joints operate in near-total silence with hardware-level stall detection, protecting the 3D-printed structure from mechanical over-travel.

---

## 📈 Roadmap

- [x] **Phase 1: Electrical System Overhaul** (32-bit Architecture, 36V Single-Bus Wiring, Hybrid Drivers)
- [ ] **Phase 2: Klipper Kinematics & 6-Axis Motion Tuning** (Custom Kinematics, Dual-Axis Alignment, Speed/Acceleration Profiling)
- [ ] **Phase 3: Embodied AI Integration** (XMOS XVF3800 Mic Array DOA + Whisper ASR + LLM Core + TTS + Servo Gripper Control)

---

## 🤝 About the Author

I am **Ivan Chuang**, a hardware developer, maker, and robotics enthusiast. This project is both a tribute to the classic BCN3D Moveo design (shoutout to *Toglefritz*, the creator of *Build a Giant 3D Printed Robot Arm*) and an exploration into bringing classic open-source hardware into the age of **Embodied AI**.

---
## 📄 License
This project is open-sourced under the MIT License. Feel free to star, fork, and contribute!
