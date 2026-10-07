# Moveo-AI: Rebirthing the Giant 3D-Printed Robot Arm with Klipper & Agentic AI

🚀 **Project Vision**
In the 2010s, I built a BCN3D Moveo and shared it on Instructables under the “Ivan Chuang made it!” section of the Build a Giant 3D Printed Robot Arm project. Now, it is time to give it a complete “brain and nervous system transplant.”

This project documents Moveo’s evolution from the traditional 8-bit Arduino / RAMPS architecture to a modern, high-performance system based on a 32-bit controller and Klipper. Future development will integrate Agentic AI, computer vision, speech recognition, and microphone-array-based DOA localization, with the goal of progressively enabling true embodied AI interaction on this classic 3D-printed robotic arm.

---

## 🛠 Phase 1: Hardware Core & Power System — Completed
The goal of this phase was to build a robust electrical foundation for the Moveo and completely replace the original 8-bit Arduino / RAMPS control architecture.

The new control cabinet has now been fully assembled, wired, powered on, and validated, providing the hardware foundation for the next stage of physical motion bring-up.

### ⚡ Key Upgrades & Hardware
- **Compute (Brain)**: Raspberry Pi 5 (8GB) with NVMe storage, powered by a dedicated industrial 5V / 20A buck converter.
- **Controller (Spine)**: FYSETC Spider V3.0 H7 (STM32H723), running Klipper and communicating with the Raspberry Pi through CAN.
- **Power System**: Mean Well LRS-350-36 36V / 350W main power supply, with dedicated 24V and 5V buck converters for the controller, computer, servo, and auxiliary loads.
- **Hybrid Motion System**:
  - **High-Power External Drives (Axis 1 & 2)**: 3 × DM556Y operating directly from the 36V rail for the high-torque motors.
  - **Ultra-Silent Onboard Drives (Axis 3 to 6)**: 3 × TMC2240 installed on the Spider and powered from the 24V rail for the remaining joints.
- **Shoulder Joint Reinforcement**: The shoulder joint uses dual NEMA 23 motors with 1:10 planetary gearboxes, providing the torque required for one of the most heavily loaded joints of the Moveo.

### 🚧 Motion Bring-Up — In Progress

Current work:

- finalize `printer.cfg`
- verify STEP / DIR / EN mappings
- verify motor direction
- configure joint parameters
- configure limit switches and homing
- validate gripper operation
- perform controlled physical joint motion

Klipper and Moonraker are installed and running on the Raspberry Pi 5, and the Spider H7 MCU has been successfully detected through CAN.


### 📐 System Architecture
Below is the electrical and communication flow of the Moveo-AI system:

```mermaid
---
config:
  layout: elk
---
flowchart TB

  subgraph PowerLayer["⚡ 動力層 Power Delivery"]
    direction LR
      PSU["Mean Well LRS-350-36<br>36V / 350W"]
      Buck5V["Buck Converter<br>36V → 5V / 20A"]
      Buck24V["Buck Converter<br>36V → 24V / 5A"]
  end

  subgraph ComputeLayer["🧠 控制中樞 Control System"]
    direction LR
      Pi5["Raspberry Pi 5 8GB<br>Ubuntu 24.04<br>Klipper Host / Moonraker"]
      Spider["FYSETC Spider V3.0 H723<br>Klipper MCU"]
  end

  subgraph SensorLayer["👀 感測裝置 Sensors"]
    direction TB
      Mic["XMOS XVF3800 Mic Array"]
      Cam["Astra Pro Plus RGB-D"]
      Lidar["RPLIDAR A1"]
  end

  subgraph ExtDrive["🦾 外接高壓驅動區 36V"]
    direction TB
      DM1["DM556Y"]
      DM2L["DM556Y"]
      DM2R["DM556Y"]

      M1(("Ax 1: Base<br>NEMA 17"))
      M2L(("Ax 2: Shoulder L<br>NEMA 23 + 1:10 Gear"))
      M2R(("Ax 2: Shoulder R<br>NEMA 23 + 1:10 Gear"))
  end

  subgraph IntDrive["🦾 Spider 內建靜音驅動區 SPI"]
    direction TB
      TMC["3× TMC2240<br>Onboard SPI"]
      M3(("Ax 3: Elbow<br>NEMA 17 + 1:19 Gear"))
      M4(("Ax 4: Wrist<br>NEMA 17"))
      M5(("Ax 5: Wrist<br>NEMA 17"))
      Servo["Gripper Servo"]
  end

  %% =========================
  %% Power Distribution
  %% =========================

  PSU == "36V Motor Power" ==> DM1 & DM2L & DM2R
  PSU == "36V" ==> Buck5V & Buck24V

  Buck5V == "5V Power" ==> Pi5
  Buck5V == "5V Servo Power" ==> Servo

  Buck24V == "24V Power" ==> Spider

  %% =========================
  %% Sensors
  %% =========================

  Mic <== "USB" ==> Pi5
  Cam <== "USB" ==> Pi5
  Lidar <== "USB" ==> Pi5

  %% =========================
  %% Host / MCU Communication
  %% =========================

  Pi5 <== "USB" ==> Spider

  %% =========================
  %% External DM556Y Drivers
  %% Spider Driver Socket:
  %% VIO 5V → Common Anode
  %% STEP / DIR / EN → Negative Inputs
  %% =========================

  Spider -. "VIO 5V<br>→ PUL+ / DIR+ / ENA+" .-> DM1 & DM2L & DM2R
  Spider -. "STEP / DIR / EN<br>→ PUL- / DIR- / ENA-" .-> DM1 & DM2L & DM2R

  %% =========================
  %% Internal TMC2240 Drivers
  %% =========================

  Spider -. "SPI Control" .-> TMC
  Spider == "24V Motor Power" ==> TMC

  %% =========================
  %% Gripper
  %% =========================

  Spider -. "PWM Signal" .-> Servo

  %% =========================
  %% Motors
  %% =========================

  DM1 == "Motor Phase" ==> M1
  DM2L == "Motor Phase" ==> M2L
  DM2R == "Motor Phase" ==> M2R

  TMC == "Motor Phase" ==> M3 & M4 & M5

  %% =========================
  %% Styles
  %% =========================

  PSU:::power
  Buck5V:::power
  Buck24V:::power

  Pi5:::compute
  Spider:::compute

  Mic:::sensor
  Cam:::sensor
  Lidar:::sensor

  DM1:::driver
  DM2L:::driver
  DM2R:::driver
  TMC:::driver

  M1:::motor
  M2L:::motor
  M2R:::motor
  M3:::motor
  M4:::motor
  M5:::motor

  Servo:::actuator

  classDef power fill:#ffe6e6,stroke:#ff3333,stroke-width:2px,color:#990000
  classDef compute fill:#e6f3ff,stroke:#0066cc,stroke-width:2px,color:#003366
  classDef driver fill:#e6ffe6,stroke:#009900,stroke-width:2px,color:#004d00
  classDef motor fill:#fff2e6,stroke:#ff9900,stroke-width:2px,color:#994c00
  classDef sensor fill:#f2e6ff,stroke:#6600cc,stroke-width:2px,color:#330066
  classDef actuator fill:#fff9e6,stroke:#cc9900,stroke-width:2px,color:#665000
```
---

## 📈 Roadmap

- **Phase 1: Electrical & Mechanical Core Overhaul — Completed**  
  Raspberry Pi 5, FYSETC Spider V3.0 H7, 36V power system, DM556Y / TMC2240 hybrid motor control, dual NEMA 23 shoulder motors with 1:10 planetary gearboxes, and the completely rebuilt control cabinet.

- **Phase 2: Klipper Motion Control & 6-Axis Bring-Up — In Progress**  
  Klipper configuration, joint-by-joint motor validation, homing and limit configuration, motor synchronization, motion calibration, speed / acceleration tuning, and reliable 6-axis physical motion.

- **Phase 3: Embodied & Agentic AI Integration — Planned**  
  RGB-D vision and object localization, coordinate transformation and inverse kinematics, Agentic AI task planning, Whisper speech recognition, microphone-array DOA localization, TTS interaction, and vision-guided robotic manipulation.

---

## 🤝 About the Author

I am **Ivan Chuang**, a hardware developer, maker, and robotics enthusiast. This project is both a tribute to the classic BCN3D Moveo design (shoutout to *Toglefritz*, the creator of *Build a Giant 3D Printed Robot Arm*) and a forward-looking exploration into bringing classic open-source hardware into the age of **Embodied AI**.

---
## 📄 License
This project is open-sourced under the MIT License. Feel free to star, fork, and contribute!


    
