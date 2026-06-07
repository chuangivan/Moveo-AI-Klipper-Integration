# Moveo-AI: Rebirthing the Giant 3D Printed Robot Arm with Klipper & LLM

🚀 **Project Vision**
In 201x, I built the BCN3D Moveo (Shared on Instructables’ “Build a Giant 3D Printed Robot Arm” as “Ivan Chuang made it!”). Now, it's time for a massive brain and neurological transplant. This project documents the complete evolution from a legacy 8-bit Arduino/RAMPS setup to a high-performance, 32-bit Klipper-based system integrated with Large Language Models (LLMs), Voice Recognition (Whisper), and Microphone-Array-based DOA (Direction of Arrival) for true Embodied AI interaction.

---

## 🛠 Phase 1: Hardware Core & Power System (Current Stage)
The goal of this phase is to establish a rock-solid electrical foundation capable of high-torque 6-axis motion, retiring the legacy 8-bit Arduino setup.

### ⚡ Key Upgrades & Hardware List
- **Compute (Brain)**: Raspberry Pi 5 (8GB) powered by a dedicated industrial 5V/20A buck converter for stable AI workloads.
- **Controller (Spine)**: FYSETC Spider V3.0 (STM32H723 @ 550MHz) running Klipper.
- **Power Supply**: Single 36V Mean Well PSU bus. A 24V buck converter is used to protect the Spider V3.0 logic circuit.
- **Hybrid Drive System**:
  - **High-Power External Drives (Axis 1 & 2)**: 3x DM556Y running directly on 36V for maximum dynamic response and torque.
  - **Ultra-Silent Onboard Drives (Axis 3 to 6)**: 4x TMC2240 via SPI communication, running on 36V for smooth, quiet, and precise end-effector control.
- **Shoulder Joint Reinforcement**: Upgraded the Shoulder (Axis 2) with dual Nema 23 (1.9 Nm) motors paired with **1:10 Planetary Gearboxes**. This amplifies the total physical torque to **~34.2 Nm**, providing extreme lifting capability and natural mechanical resistance to prevent gravity drops during power loss.

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
        Buck5V["Buck Converter<br>36V to 5V / 20A"]
        Buck24V["Buck Converter<br>36V to 24V / 5A"]
  end
 subgraph ComputeLayer["🧠 運算中樞 Control & AI Brain"]
    direction LR
        Pi5["Raspberry Pi 5 8GB<br>Mainsail, OpenAI API, DOA"]
        Spider["FYSETC Spider V3.0 H723<br>Klipper MCU"]
  end
 subgraph SensorLayer["👀 感官終端 AI Sensors"]
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
        M1(("Ax 1: Base Nema 17"))
        M2L(("Ax 2: Shoulder L Nema 23 + 1:10 Gear"))
        M2R(("Ax 2: Shoulder R Nema 23 + 1:10 Gear"))
  end
 subgraph IntDrive["🦾 內建靜音驅動區 SPI"]
    direction TB
        TMC["3x TMC2240<br>Onboard SPI"]
        M3(("Ax 3: Elbow Nema 17 + 1:19 Gear"))
        M4(("Ax 4: Wrist Nema 17"))
        M5(("Ax 5: Wrist Nema 17"))
        Servo["Gripper Servo"]
  end
    PSU == 36V 直連 ==> DM1 & DM2L & DM2R & TMC
    PSU == 36V 降壓 ==> Buck5V & Buck24V
    Buck5V == 5V 雙排針供電 ==> Pi5
    Buck24V == 24V 邏輯與抱閘供電 ==> Spider
    Mic <== "USB / USB 3.0 Data" ==> Pi5
    Cam <== "USB / USB 3.0 Data" ==> Pi5
    Lidar <== "USB / USB 3.0 Data" ==> Pi5
    Pi5 <== USB Data ==> Spider
    Spider -. 5V Pulse/Dir 共陽極 .-> DM1 & DM2L & DM2R
    Spider -. SPI 總線通訊 .-> TMC
    Spider -. 24V MOS 抱閘控制 .-> M2L & M2R
    Spider -. 5V PWM .-> Servo
    DM1 == Power Phase ===> M1
    DM2L == Power Phase ===> M2L
    DM2R == Power Phase ===> M2R
    TMC == Power Phase ===> M3 & M4 & M5

     PSU:::power
     Buck5V:::power
     Buck24V:::power
     Pi5:::compute
     Spider:::compute
     Mic:::ai
     Cam:::ai
     Lidar:::ai
     DM1:::driver
     DM2L:::driver
     DM2R:::driver
     M1:::motor
     M2L:::motor
     M2R:::motor
     M3:::motor
     M4:::motor
     M5:::motor
     Servo:::driver
    classDef power fill:#ffe6e6,stroke:#ff3333,stroke-width:2px,color:#990000
    classDef compute fill:#e6f3ff,stroke:#0066cc,stroke-width:2px,color:#003366
    classDef driver fill:#e6ffe6,stroke:#009900,stroke-width:2px,color:#004d00
    classDef motor fill:#fff2e6,stroke:#ff9900,stroke-width:2px,color:#994c00
    classDef ai fill:#f2e6ff,stroke:#6600cc,stroke-width:2px,color:#330066
```

---

## 📈 Roadmap

- [x] **Phase 1: Electrical & Mechanical Core Overhaul** (32-bit Mainboard, 36V Single-Bus, Brake Motors, 1:10 Planetary Gearbox)
- [ ] **Phase 2: Klipper Kinematics & 6-Axis Motion Tuning** (Custom Robot Kinematics, Dual-Axis Alignment, Speed/Acceleration Profiling, Soft Limits Configuration)
- [ ] **Phase 3: Embodied AI Integration** (XMOS XVF3800 Mic Array DOA + Whisper ASR + LLM Intent Parsing + TTS Response + Servo Gripper)

---

## 🤝 About the Author

I am **Ivan Chuang**, a hardware developer, maker, and robotics enthusiast. This project is both a tribute to the classic BCN3D Moveo design (shoutout to *Toglefritz*, the creator of *Build a Giant 3D Printed Robot Arm*) and a forward-looking exploration into bringing classic open-source hardware into the age of **Embodied AI**.

---
## 📄 License
This project is open-sourced under the MIT License. Feel free to star, fork, and contribute!


    
