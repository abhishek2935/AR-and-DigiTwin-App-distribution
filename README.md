<div align="center">
  <img src="https://placehold.co/1200x300/000000/FFFFFF/png?text=AR-PULSE:+Industry+4.0+Ecosystem" alt="AR-Pulse Banner">

  <h1>🏭 AR-Pulse: AR-Enabled Fault Detection & Digital Twin Sync</h1>

  **Award-Winning Automation 4.0 Ecosystem • Ranked 11th Globally on ElectronicWings**

  <p align="center">
    <img src="https://img.shields.io/badge/Unity-2021.3_LTS-black?style=for-the-badge&logo=unity" alt="Unity">
    <img src="https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=c-sharp&logoColor=white" alt="C#">
    <img src="https://img.shields.io/badge/ESP32-C++-red?style=for-the-badge&logo=espressif" alt="ESP32">
    <img src="https://img.shields.io/badge/Arduino-Nano-00979D?style=for-the-badge&logo=arduino" alt="Arduino">
    <img src="https://img.shields.io/badge/Firebase-Realtime_DB-FFCA28?style=for-the-badge&logo=firebase" alt="Firebase">
    <img src="https://img.shields.io/badge/ARCore-Google-4285F4?style=for-the-badge&logo=google" alt="ARCore">
    <img src="https://img.shields.io/badge/License-GPL_v3-blue.svg?style=for-the-badge" alt="License">
  </p>

  <p align="center">
    <b>Bridging the gap between physical hardware, edge intelligence, and immersive XR visualization.</b>
  </p>
</div>

---

## 📑 Table of Contents
1. [Project Overview](#-project-overview)
2. [Core Innovations](#-core-innovations)
3. [System Architecture](#-system-architecture)
4. [Hardware & Bill of Materials](#-hardware--bill-of-materials)
5. [Hardware Build Guide](#-hardware-build-guide)
    - [The Feedback Servo Hack](#1-the-feedback-servo-hack)
    - [Wiring & Schematics](#2-wiring--schematics)
6. [Firmware & Edge Computing (Phase 2)](#-firmware--edge-computing-phase-2)
7. [Application Layer: Unity & AR (Phase 3)](#-application-layer-unity--ar-phase-3)
8. [Setup & Installation](#-setup--installation)
9. [Error State Dictionary](#-error-state-dictionary)
10. [The Team](#-the-team)

---

## 📖 Project Overview

While most tutorials stop at basic IoT dashboards, **AR-Pulse** is engineered as a complete, industrial-grade ecosystem. We designed this system to demonstrate true **Automation 4.0** principles by capturing precise physical data from a 6-Degrees-of-Freedom (6 DoF) robotic arm, processing it at the edge using an ESP32, and syncing it to a live Unity-based 3D Digital Twin.

### The Problem
Standard hobby servos and older industrial actuators operate in "open-loop" systems. A controller sends a target position, but has no way of verifying if the physical hardware actually reached that position due to physical jams, wear, or load. 

### The Solution
We physically modified standard actuators to expose their internal potentiometers, creating a closed-loop feedback system. This absolute position data is fed over industrial RS485 to an ESP32 edge device, which calculates real-time faults. Finally, operators can use an AR tablet/headset to overlay this live diagnostic data (joint angles, temperatures, fault states) directly onto the physical machine.

<div align="center">
  <img src="https://placehold.co/400x300/222222/FFFFFF/png?text=Digital+Twin+Sync+GIF" alt="Digital Twin Sync" width="45%">
  &nbsp; &nbsp;
  <img src="https://placehold.co/400x300/222222/FFFFFF/png?text=AR+Diagnostic+Overlay+GIF" alt="AR Overlay" width="45%">
</div>

---

## ✨ Core Innovations

* 🧠 **Real-Time Edge Fault Detection:** Instead of relying on slow cloud processing, the ESP32 calculates delta (velocity) locally to detect stalls, noise, and range errors in milliseconds.
* 🌍 **Live 3D Digital Twin:** Not a static model. A live simulation receiving continuous data streams to mirror physical reality, allowing for remote monitoring and predictive maintenance.
* 📱 **Augmented Reality Diagnostics:** Reduces maintenance time by projecting virtual UI elements (error heatmaps, joint statuses) seamlessly over real-world machinery.
* ⚡ **High-Speed Binary Ingestion:** Bypasses slow text parsing by utilizing a custom `0xAA` header protocol to ingest raw 50-byte binary arrays over RS485, reconstructed instantly via memory copying (`memcpy`).

---

## 🚀 System Architecture

<div align="center">
  <img src="https://github.com/abhishek2935/AR-and-DigiTwin-App-distribution/blob/main/assets/sys_arch.jpg" alt="Architecture Diagram">
</div>

1. **Physical Layer:** 6 DoF Robot Arm $\rightarrow$ Custom Feedback Servos $\rightarrow$ Arduino Nano $\rightarrow$ RS485.
2. **Edge Layer:** RS485 $\rightarrow$ ESP32-C6 $\rightarrow$ Edge Logic Parsing $\rightarrow$ JSON Formatting $\rightarrow$ Wi-Fi 6.
3. **Cloud Layer:** Secure Google Firebase Realtime Database.
4. **App Layer:** Unity App $\rightarrow$ ARCore Anchoring $\rightarrow$ Digital Twin Visualization.

---

## 🧰 Hardware & Bill of Materials

### Primary Electronics
| Component | Qty | Description / Purpose |
| :--- | :---: | :--- |
| **ESP32-C6 DevKitC-1-N8** | 1 | High-speed Wi-Fi 6 edge processing unit. |
| **Arduino Nano** | 1 | Local hardware controller & data aggregator. |
| **RS485 to TTL Converter** | 2 | Industrial serial communication between Nano & ESP32. |
| **Logic Level Converter** | 1 | Bridges 5V Arduino logic to 3.3V ESP32 logic. |
| **PCA9685** | 1 | 16-Channel PWM Servo Driver. |
| **MG995 / SG90 Servos** | 6 | Actuators for the 6 DoF arm. |
| **4x 1.5V Battery Pack** | 1 | Dedicated high-current power supply for servos. |

### Structural Components
* Fully 3D Printed 6 DoF Robotic Arm Chassis *(STL files in repository)*.
* 1x Ball Bearing `6806ZZ` (30x42x7).
* 5x M2 Screws & 5x M3 Screws.
* 5x ANSI B18.6.4 No.3-28 1/2″ Cross Recessed Truss Head Screws.

---

## 🛠️ Hardware Build Guide

### 1. The Feedback Servo Hack
To achieve true Digital Twin synchronization, the controller must know the *exact* real-world position of the arm. We "hack" standard servos to expose their internal voltage divider:

1. **Open the Servo:** Remove the four screws at the bottom of the MG995/SG90 housing.
2. **Locate the Potentiometer:** Identify the three wires connected to the internal potentiometer.
3. **Solder the Wiper:** Solder a new, differently colored wire (e.g., yellow or white) directly to the middle pin (the wiper) of the potentiometer.
4. **Reassemble:** Route the new feedback wire out of the casing and close the servo. This wire now provides an analog voltage proportional to absolute position.

### 2. Wiring & Schematics

<details>
<summary><b>🔌 Click to expand detailed wiring instructions</b></summary>

**Power Supply & Common Ground**
* Battery (+) $\rightarrow$ PCA9685 Terminal (+)
* Battery (-) $\rightarrow$ PCA9685 Terminal (-)
* *Ensure all system grounds (Arduino, ESP32, PCA9685, Battery) are tied together.*

**Logic Level Converter (LLC)**
* **References:** ESP32 3.3V $\rightarrow$ LLC LV | Arduino 5V $\rightarrow$ LLC HV | System GND $\rightarrow$ LLC GND
* **TX/RX:** * ESP32 TX $\rightarrow$ LLC LV1 $\rightarrow$ LLC HV1 $\rightarrow$ Arduino RX (D0)
  * Arduino TX (D1) $\rightarrow$ LLC HV2 $\rightarrow$ LLC LV2 $\rightarrow$ ESP32 RX

**Servo Driver (PCA9685) to Arduino**
* Arduino 5V $\rightarrow$ PCA9685 VCC
* Arduino A4 (SDA) $\rightarrow$ PCA9685 SDA
* Arduino A5 (SCL) $\rightarrow$ PCA9685 SCL

**Servo Feedback (Position Sensing)**
Connect the newly soldered "feedback wires" to the Arduino Analog pins:
* Servo 1 (Base) $\rightarrow$ `A0`
* Servo 2 (Shoulder) $\rightarrow$ `A1`
* Servo 3 (Elbow) $\rightarrow$ `A2`
* Servo 4 (Wrist Pitch) $\rightarrow$ `A3`
* Servo 5 (Wrist Roll) $\rightarrow$ `A6`
* Servo 6 (Gripper) $\rightarrow$ `A7`
</details>

---

## 🧠 Firmware & Edge Computing (Phase 2)

The intelligence of AR-Pulse resides in the ESP32. Instead of bombarding the cloud with raw sensor values, the ESP32 acts as an edge node.

1. **Binary Data Ingestion:** The `uartTryReadPacketAndParse` function waits for a sync header (`0xAA`) and reads exactly 50 bytes of binary payload.
2. **Checksum Verification:** Calculates an XOR sum of the payload. If it doesn't match the received checksum byte, the packet is instantly discarded to prevent noisy jumps in the Digital Twin.
3. **Edge Logic (`checkErrors`):**
   Continuous comparison of `Target` vs `Actual` angles to calculate $\Delta$ (delta). 
   *If $\Delta$ deviates beyond thresholds, a specific error code is assigned (See [Error State Dictionary](#-error-state-dictionary)).*
4. **Cloud Formatting:** Every 500ms, the verified and processed arrays are converted into JSON strings (e.g., `[90, 45, 120, ...]`) and pushed to Firebase.

---

## 🎮 Application Layer: Unity & AR (Phase 3)

The visualization layer is built on Unity 2021.3 LTS, utilizing Firebase for real-time telemetry and ARCore for spatial anchoring.

### Core Scripts
* `FirebaseReader.cs`: Authenticates securely, subscribes to the Firebase Realtime Database `ValueChanged` event, and extracts the JSON arrays into public C# float arrays.
* `RobotJointController.cs`: Reads the `actualValues` from the Reader script and applies `Quaternion.Euler` rotations to the local joints of the 3D Prefab.

### Unity Setup for AR
We utilize the **New Input System**, AR Foundation, and ARCore XR Plugin. Android Build Support is required.
* **Camera Setup:** Standard Main Camera is replaced with `AR Session` and `AR Session Origin` (or `XROrigin`).
* **Visual Alerts:** Error codes pulled from Firebase trigger dynamic material changes in Unity (e.g., `101` turns the specific mesh outline red).

---

## 💻 Setup & Installation

### 1. Cloud Setup (Firebase)
1. Create a [Firebase Project](https://console.firebase.google.com/).
2. Enable **Authentication** (Email/Password).
3. Create a **Realtime Database** (Set region to your closest server, e.g., Singapore) and start in Test Mode.
4. Copy your Database URL and Web API Key.

### 2. Firmware Deployment
1. Open `nano_transmit_code.ino` in the Arduino IDE. Flash to the Arduino Nano.
2. Open `esp32_recieve_code.ino`. 
3. Replace the placeholder credentials:
   ```cpp
   #define WIFI_SSID "Your_SSID"
   #define WIFI_PASSWORD "Your_Password"
   #define FIREBASE_API_KEY "YOUR_WEB_API_KEY"
   #define FIREBASE_DB_URL "YOUR_DB_URL"
   4. Flash the code to the ESP32-C6.

### 3. Unity & AR Deployment
1. Install **Unity Hub** and **Unity 2021.3 LTS**.
2. Clone this repo and open the Unity project.
3. Download `google-services-desktop.json` (for PC) or `google-services.json` (for Android) from Firebase and place it in the `Assets/` folder.
4. Import the Firebase Unity SDK (`FirebaseDatabase.unitypackage`). Click **Resolve Dependencies**.
5. Go to **Build Settings** ➔ **Switch Platform** to Windows (for Desktop Twin) or Android (for AR).
6. Build and Run!

---

## 🚨 Error State Dictionary

The ESP32 classifies physical anomalies into the following system states, which dictate the AR visual overlay:

| Error Code | Designation | Trigger Condition | System Action |
| :---: | :--- | :--- | :--- |
| **0** | `SYSTEM_NOMINAL` | Physical pos matches Target pos within ± tolerance. | Green AR Overlay. Normal operation. |
| **2** | `ERR_STALL` | Target changed, but Physical hasn't moved for >400ms. | Indicates physical jam or heavy load. |
| **4** | `ERR_NOISE` | Position jumped impossibly fast (>10° per loop cycle). | Discards frame. Usually indicates wiring fault. |
| **5** | `ERR_RANGE` | Analog feedback value outside 0-300 physical limit. | Triggers critical hardware inspection alert. |

---

## 🤝 The Team

Engineered and developed by students at **MIT World Peace University (Pune, India)**.

* 👨‍💻 **Vedant Jadhav** - Firmware, Edge Logic, Servo Modifications & HW Integration
* 👨‍💻 **Abhishek Patil** - Unity Application & AR Development
* 👨‍💻 **Amey Ganorkar** - Hardware Assembly & Firmware Dev
