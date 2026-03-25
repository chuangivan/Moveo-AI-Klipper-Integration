# Moveo-AI: Rebirthing the Giant 3D Printed Robot Arm with Klipper & LLM

### 🚀 Project Vision
In 201x, I built the BCN3D Moveo (Shared on Instructables’ “[Build a Giant 3D Printed Robot Arm](https://www.instructables.com/Build-a-Giant-3D-Printed-Robot-Arm/)” as “Ivan Chuang made it!”). Now, in 2026, it's time for a massive brain transplant. This project documents the transition from an 8-bit Arduino/RAMPS setup to a high-performance 32-bit Klipper system integrated with LLM (OpenAI), Voice Recognition (Whisper), and Mic-Array-based DOA (Direction of Arrival).

---

## 🛠 Phase 1: Hardware Core & Power System (Current Stage)

The goal of this phase is to establish a rock-solid electrical foundation capable of high-torque 6-axis motion.

### Key Upgrades:
- **MCU**: Replaced Arduino Mega 2560 + RAMPS 1.4 with **FYSETC Spider V3.0 (STM32H723)**.
- **Power Supply**: Upgraded to **Mean Well LRS-350-36 (36V)** for enhanced stepper motor performance.
- **Step-Down**: Added a high-current DC-DC Buck Converter (36V to 24V) to safely power the MCU and fans.
- **Drivers**: Re-wiring industrial-grade **DM556Y** (Base/Shoulder) and **DM420Y** (Elbow/Wrist) using Common Anode configuration.

### Why 36V?
Higher voltage allows the NEMA 17/23 motors to maintain torque at higher speeds, which is crucial for the dynamic response needed in AI-driven interactions.

---

## 📈 Roadmap
- [x] Phase 1: Electrical System Overhaul (Hardware Selection & Wiring)
- [ ] Phase 2: Klipper Kinematics & 6-Axis Motion Tuning
- [ ] Phase 3: AI Integration (Mic Array DOA + Whisper + OpenAI + TTS)

---

## 🤝 About the Author
I am Ivan Chuang, a maker and robotics enthusiast. This project is a tribute to the classic Moveo design and to Toglefritz, the creator of [Build a Giant 3D Printed Robot Arm](https://www.instructables.com/Build-a-Giant-3D-Printed-Robot-Arm/), bringing these inspirations into the age of Embodied AI.

