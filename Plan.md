# 🚀 Smart PX4 Autopilot Agent with LangGraph & MAVSDK

## 📌 Project Overview
This project develops a **smart, autonomous PX4 Autopilot agent** powered by **LangGraph** (LLM orchestration) and **MAVSDK** (PX4 control interface).  
The agent:
- Executes **missions step by step** with verification.  
- Uses **low-level MAVSDK skills** (arm, move, land, etc.) as tools.  
- Builds **high-level autonomous skills** (search, survey, object tracking, crowd counting).  
- Continuously monitors **PX4 telemetry & vision sensors** to ensure **realistic mission success**.  
- Reacts to environment (obstacles, failures) using **ReAct-style reasoning loops**.  

---

## 🏗️ System Architecture

![System architecture diagram](diagram.png)

**Layers**:
1. **High-Level Reasoning (LangGraph Agent)**  
   - Parses mission goals → breaks down into subtasks  
   - Uses **ReAct reasoning loop (Thought → Action → Observation → Re-Plan)**  
   - Ensures mission progress until completion  

2. **Perception & Environment Awareness**  
   - Vision data from OAK-D Pro (YOLO detections with X, Y, Z + labels)  
   - Telemetry from PX4 sensors (altitude, battery, GPS, velocity, health checks)  
   - Obstacle avoidance using collision prevention & vision pipelines  

3. **Low-Level Control (MAVSDK Tools)**  
   - Arm/disarm, takeoff, land  
   - Offboard velocity & position control  
   - Mission uploads (waypoints, surveys)  
   - Real-time telemetry checks  

---

## 🎯 Project Plan

| Phase | Tasks |
|-------|-------|
| **1. Setup** | Connect PX4 ↔ Jetson/Companion computer using MAVSDK (serial/UDP). Verify telemetry streams. |
| **2. Low-Level Skills** | Implement MAVSDK tool functions (arm, move, circle, orbit, return-to-launch, offboard velocity). |
| **3. High-Level Skills** | Build autonomous skills (survey, object tracking, crowd counting, obstacle avoidance) on top of low-level primitives. |
| **4. LangGraph Agent** | Design custom graph with ToolNodes & conditional loops for persistent sensor checks. |
| **5. Mission Execution** | Test autonomous missions: “search for people”, “count cars”, “find bottle”, “track object”, “survey area”. |
| **6. Advanced Features** | Swarm coordination, dynamic replanning, adaptive autonomy for contested environments. |

---

## 🛠️ Skills Breakdown

### 🔹 Low-Level (MAVSDK Tools)
- `arm()` / `disarm()`
- `takeoff(altitude)`
- `land()`
- `move_ned(x, y, z, yaw)`
- `move_body(forward, right, up, yaw_rate)`
- `circle(radius, speed)`
- `check_battery()`
- `get_altitude()`
- `get_health_all_ok()`

### 🔹 High-Level (Agent Composed)
- **Survey Area** → break region into grid → fly each waypoint → collect imagery.  
- **Search & Detect** → fly patrol → run YOLO inference → log detections.  
- **Count People** → aggregate YOLO detections → apply crowd-count model.  
- **Object Tracking** → continuously center target in camera → send offboard velocity corrections.  
- **Obstacle Avoidance** → use collision prevention + depth vision → reroute.  

---

## 🔄 Workflow

1. **Mission Request (Natural Language)**  
   Example: *“Drone, search the area and count people.”*  

2. **LangGraph Planning**  
   - Decomposes into subtasks: takeoff → survey grid → detect objects → count people → land.  

3. **Execution (ReAct Loop)**  
   - `Thought`: “Need altitude confirmation.”  
   - `Action`: Call `telemetry.altitude()`.  
   - `Observation`: Returns 0.1m (not flying).  
   - `Re-Plan`: Retry takeoff until 3m confirmed.  

4. **Adaptive Replanning**  
   - If obstacle detected → switch path.  
   - If target lost → widen search area.  

---

## 🧩 Example LangGraph Design (Pseudo-Code)

```python
from langgraph.prebuilt import create_react_agent, ToolNode
from mavsdk import System

# Define MAVSDK low-level tools
async def takeoff_tool(alt=3.0): ...
async def move_ned_tool(x,y,z,yaw): ...
async def land_tool(): ...
async def telemetry_tool(): ...

tools = [takeoff_tool, move_ned_tool, land_tool, telemetry_tool]

# Create ReAct Agent with sensor feedback loops
agent = create_react_agent(
    model="gpt-5",
    tools=tools,
    state_schema={
        "mission_step": str,
        "altitude": float,
        "battery": float,
        "detections": list
    },
    checkpointer=True
)

# Run a mission
mission = "Survey the field and count the number of people"
agent.invoke({"input": mission})
````

---

## 📊 Example Mission Table

| Mission                | Decomposition                                     | Skills Used                                        |
| ---------------------- | ------------------------------------------------- | -------------------------------------------------- |
| **Search for People**  | Takeoff → Survey grid → Detect → Count → Land     | `takeoff`, `move_ned`, `telemetry`, YOLO detection |
| **Survey Area**        | Upload grid → Execute waypoints → Capture imagery | `mission_upload`, `camera_trigger`, `telemetry`    |
| **Object Tracking**    | Lock target → Adjust velocity → Loop until lost   | `offboard_velocity`, vision feedback               |
| **Obstacle Avoidance** | Monitor lidar/depth → Stop/re-route               | `telemetry`, collision prevention                  |
| **Find Bottle**        | Patrol area → Detect bottle object                | `survey`, YOLO inference                           |

---

## 🚦 Safety & Monitoring

* **Health Check**: `telemetry.health_all_ok` before every step.
* **Failsafe**: Offboard control requires 2Hz setpoints; otherwise PX4 exits mode.
* **Collision Prevention**: PX4 built-in prevention + external vision.
* **Battery Guard**: Auto-RTL when battery < threshold.

---

## 📌 Future Work

* Multi-agent swarm coordination
* Adaptive mission replanning using RL
* Onboard fine-tuned LLMs for edge autonomy
* Sensor fusion (radar + vision + GPS-denied nav)

---

## 🤝 Acknowledgments

* [PX4 Autopilot](https://px4.io)
* [MAVSDK](https://mavsdk.mavlink.io)
* [LangGraph](https://langchain-ai.github.io/langgraph/)

---

