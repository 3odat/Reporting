# Autonomous PX4 Drone with Real-Time Vision & Sensor Fusion

## Overview
This project integrates the **PX4 Autopilot** (Holybro X500 V2) with **Jetson Orin Nano** and **OAK-D Pro** to enable **real-time object detection, 3D spatial awareness, and sensor fusion**.  
Our next milestone is building a **LangGraph-based Smart Agent** capable of executing complex missions via **natural language commands** and **autonomous decision-making**.

---

## 1. Hardware Platform

- **Airframe:** Holybro X500 V2
- **Flight Controller:** Pixhawk (PX4 firmware)
- **Companion Computer:** NVIDIA Jetson Orin Nano
- **Vision System:** Luxonis OAK-D Pro (RGB + Stereo Depth)
- **GNSS:** M10 Multi-constellation GPS (GPS, Galileo, GLONASS, BeiDou)
- **Power & Safety:** Bench testing with no propellers for development phase

---

## 2. Vision Integration (YOLOv6 + Depth)

We successfully integrated **YOLOv6** with the **OAK-D Pro** to:
- Detect and classify objects in real-time
- Extract 2D coordinates (X, Y) from RGB camera
- Retrieve depth (Z) from stereo sensor for **3D spatial positioning**

Example detection output:

![Vision Output](outside_sensors_Yolo.png)

---

## 3. PX4 Internal Sensor Data via MAVSDK

Using **MAVSDK-Python**, we accessed and streamed key PX4 telemetry data:
- **Battery Voltage & Percentage**
- **GPS Position & Health**
- **Altitude & Velocity**
- **Flight Mode & Arming State**

This data is essential for mission safety and will be fused with vision data for **context-aware navigation**.

![Internal Sensors](internal_sensors.png)

---

## 4. GPS Outdoor Testing

- M10 GNSS locked onto multiple constellations with strong signal quality.
- Successfully passed PX4 preflight GPS checks.
- Achieved consistent satellite fixes in field environments.


---

## 5. Jetson ↔ PX4 Serial Communication

- Connected via `/dev/ttyACM0` (USB CDC ACM mode) at 115200 baud.
- Successfully tested MAVSDK commands **physically without propellers** for safety.
- Example tested commands:
  - `arm()`
  - `takeoff()` to a safe altitude
  - `land()`


---

## 6. Next Step — LangGraph Smart Agent

We are now moving towards a **LangGraph-powered autonomous agent** that will:
- Accept **natural language mission prompts**
- Continuously read **vision detections** and **sensor telemetry** in real-time
- Perform **sensor fusion** to make context-aware decisions
- Execute multi-step mission plans automatically
- Provide a **dashboard** for live mission status

Planned high-level capabilities:
- Obstacle avoidance using vision + lidar/stereo depth
- Object tracking and approach
- Area survey and mapping
- Event-triggered actions (e.g., "If person detected within 3m, hover and record video")


---

## 7. Dashboard & Data Visualization

We will design a **web-based dashboard** to:
- Show live RGB + depth video stream
- Display MAVSDK telemetry in real-time
- Render mission plan execution status
- Log and playback mission events


---

## Roadmap

| Stage | Task | Status |
|-------|------|--------|
| 1 | PX4 + Jetson hardware setup | ✅ |
| 2 | Vision pipeline (YOLOv6 + depth) | ✅ |
| 3 | MAVSDK telemetry integration | ✅ |
| 4 | GPS testing & preflight | ✅ |
| 5 | Safe serial control tests | ✅ |
| 6 | LangGraph agent design | 🚧 |
| 7 | Real-time dashboard | 🚧 |
| 8 | Outdoor autonomous flight | ⏳ |

---

## License
MIT License. See [LICENSE](LICENSE) for details.
