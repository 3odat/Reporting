# 🚁 Smart PX4 Autopilot Agent with LangGraph + MAVSDK

## Overview
This project explores a **novel AI-driven agent** that integrates:
- **PX4 Autopilot + MAVSDK** for reliable low-level flight control.
- **LangGraph (LLM-based planning)** for high-level reasoning, mission orchestration, and step tracking.
- **Onboard vision (YOLO / OAK-D Pro)** for real-time perception (3D object detection with X, Y, Z coordinates).

The goal is a **smart drone agent** that can autonomously:
- Understand high-level missions (e.g., *“Search for people in this area and report back”*).
- Decompose them into **low-level skills** (takeoff, move, circle, land, detect, count).
- Continuously verify each action with **sensor and telemetry feedback**.
- **Replan on the fly** if obstacles, targets, or anomalies appear.
- Complete missions safely and intelligently, like a human pilot would.

---

## 🔹 Architecture
- **LangGraph Agent (ReAct-style):** Uses reasoning → action → observation loops to plan and execute steps until mission completion.
- **MAVSDK Tools:** Expose PX4 actions as callable tools (`takeoff()`, `goto_location()`, `land()`, `get_battery()`, `capture_photo()`, `detect_object()`, etc.).
- **PX4 Autopilot:** Ensures real-time stability, GPS navigation, and failsafes.
- **Companion Computer (Jetson Orin Nano):** Runs the LLM agent, tool functions, and perception models.

---

## 🔹 Skills

### Low-Level (Tools via MAVSDK)
- `arm_and_takeoff(alt)`
- `goto_location(lat, lon, alt)`
- `set_velocity(vx, vy, vz)`
- `orbit(center, radius)`
- `land()`
- `get_altitude()`, `get_battery()`, `get_distance_ahead()`
- `capture_photo()`, `detect_object(image, type)`, `count_people(image)`

### High-Level (Agent-Driven Missions)
- **Search & Rescue:** grid search with object/person detection.
- **Survey/Mapping:** lawnmower flight patterns with image capture.
- **People Counting:** aerial observation, counting with vision.
- **Object Finding:** detect and localize bottles, food, or other targets.
- **Target Tracking:** follow moving objects with live updates.
- **Obstacle Avoidance:** monitor distance sensors and replan paths.

---

## 🔹 Mission Workflow
1. **Initialize mission** → Agent plans steps.
2. **Takeoff** → Verify altitude via sensors.
3. **Navigate & Observe** → Move to waypoints, capture images, detect targets.
4. **Replan if needed** → Avoid obstacles, adjust altitude, change path.
5. **Loop until goal met** → Cover entire area, confirm detections.
6. **Complete mission** → Return-to-launch (RTL) or land safely.
7. **Report results** → Summarize mission outcome.

---

## 🔹 Why This Approach?
- **MAVSDK** gives safe, reliable low-level control.
- **LangGraph ReAct Agent** enables flexible, multi-step reasoning with tool use【LangGraph Docs】.
- **Sensor-driven verification** ensures each step is actually completed (no blind trust in function calls).
- **Adaptive autonomy**: the agent can react like a human—checking, retrying, and replanning when things don’t go as expected.

---

## 🔹 Applications
- ISR (Intelligence, Surveillance, Reconnaissance)
- Search & Rescue
- Autonomous Survey & Mapping
- Humanitarian Aid (locating people, supplies)
- Military or defense missions requiring autonomy and adaptability

---

## ✅ Key Takeaway
This project demonstrates how **LLM agents + PX4 autopilot** can achieve **next-gen autonomy**:
- Start-to-finish mission execution.
- Human-like reasoning and adaptability.
- Modular, extensible, and explainable.

> With this architecture, a single drone agent can handle diverse missions simply by changing the high-level instruction and available tools.
