# Semantic Warehouse Inspection — development package

**Start here.** This package contains implemented code and genuine local evidence. It is not yet a complete assessment submission: the Colab public link, TheConstruct Rosject, ROS2 simulation evidence and final report remain outstanding.

## Contents and verification

| Item | Current status |
|---|---|
| Task 1 notebook | Python code cells executed locally in order; all assertions passed; three views embedded |
| Task 1 camera images | Rendered by PyBullet DIRECT/TinyRenderer; visually reviewed |
| ROS2 controller/package | Implemented; syntax checked; ROS2 dependencies unavailable locally |
| ROS2 controller tests | 14 offline unit tests passed |
| Closed-loop mission | Passed in a synthetic kinematic harness; not Gazebo or ROS2 evidence |
| Gazebo warehouse launch | Implemented for Humble/Gazebo Classic; integration not executed |
| TheConstruct Rosject | Suggested name provided; no account deployment performed |
| Report | Completion worksheet provided; final submission not written |

The notebook's installation cell was skipped during local verification because dependencies were installed beforehand. Remaining Python cells executed in a shared namespace under Python 3.11.16, PyBullet 3.2.7, NumPy 2.4.6 and Matplotlib 3.11.2. This was direct cell execution, not a live Colab run. The Jupyter kernel could not start under the local network restrictions; direct execution then succeeded. See evidence/task1_execution.txt.

## Task 1: Google Colab

1. Open https://colab.research.google.com and upload Semantic_Warehouse_Task1.ipynb.
2. Select a Python runtime and choose Runtime > Run all.
3. Confirm that all checks pass and all three images display. Save the notebook to your Drive.
4. Enable viewer access for anyone with the link, if your institution permits it. Test the link while signed out.
5. Add the verified notebook link to the final report. Do not submit a local path as the link.

## Task 2: TheConstruct setup

Target platform: **ROS2 Humble with Gazebo Classic**, not ROS1 Noetic or modern Gazebo/ros-gz. Choose a Rosject with the ROS2 TurtleBot3 Gazebo packages installed. Use the proposed name in ROSJECT_NAME.txt and verify it is available. This name is not an existing deployed project.

Upload/extract this package into /home/user/rsp_project and open Task2_Runbook.ipynb inside the Rosject. The commands below assume that exact directory. If the extracted folder differs, adjust that one path.

### Terminal 1: dependency preflight and build

```bash
source /opt/ros/humble/setup.bash
ros2 pkg prefix gazebo_ros
ros2 pkg prefix turtlebot3_gazebo
cd /home/user/rsp_project/ros2_ws
colcon build --symlink-install --packages-select warehouse_inspector
source install/setup.bash
ros2 launch warehouse_inspector simulation.launch.py
```

Keep Terminal 1 open. Open Gazebo's graphical window and confirm a Burger robot at the home marker, two inspection markers, shelves, barriers and landmarks. The launch file uses the installed TurtleBot3 Burger SDF. If preflight fails, use a matching Rosject; these instructions do not install or repair the hosted environment automatically. Do not launch two copies of the simulation.

### Terminal 2: verify interfaces, then launch the controller

```bash
source /opt/ros/humble/setup.bash
source /home/user/rsp_project/ros2_ws/install/setup.bash
ros2 topic list -t
ros2 topic info /scan --verbose
ros2 topic info /odom --verbose
ros2 topic info /cmd_vel --verbose
ros2 launch warehouse_inspector mission.launch.py
```

Required message types are sensor_msgs/msg/LaserScan, nav_msgs/msg/Odometry and geometry_msgs/msg/Twist respectively. Names can be changed in config/mission.yaml. A TwistStamped interface requires an adapter; do not use this node unchanged in that environment. Only this controller should command velocity during a mission. Sensor subscribers use the sensor-data QoS profile.

### Terminal 3: keyboard interface

```bash
source /opt/ros/humble/setup.bash
source /home/user/rsp_project/ros2_ws/install/setup.bash
ros2 run warehouse_inspector keyboard
```

Type `start` and press Enter. Other commands are `pause`, `resume`, `cancel`, `reset`, and `quit`. The keyboard is a line-based interface so it works in browser terminals. `quit` sends cancel. Start requires fresh, valid scan and odometry. A FAULT needs `reset` followed by `start`; it never resumes automatically.

### Terminal 4: monitor and capture evidence

```bash
source /opt/ros/humble/setup.bash
source /home/user/rsp_project/ros2_ws/install/setup.bash
ros2 topic echo /inspection/status
```

CSV logs are written to /home/user/inspection_evidence. Save screenshots of IDLE, navigation, inspection, avoidance and COMPLETE with the relevant terminal visible. Export the actual CSV and copy it into evidence/theconstruct; do not replace it with the offline test CSV.

### Required execution checks

- Normal mission: two inspection points, then home; expect COMPLETE. Inspect the trajectory and CSV.
- Pause/resume: pause while moving, verify zero velocity command, then resume.
- Cancel: cancel while moving, verify IDLE and zero velocity command.
- Obstacle: use Gazebo's object insertion tool to place a box ahead. Verify the stop and AVOID transition. Record recovery or FAULT honestly. Capture the box position.
- Stale sensors: pause Gazebo physics via its UI while the mission is active. The wall-clock watchdog should stop commands and enter FAULT after the sensor timeout. Unpause, reset and rerun. Availability of pause services varies by environment.
- Unreachable goal: select a blocked goal and record avoidance or mission timeout. Do not claim a global planner will find an alternative route.

## Behaviour and limitations

The same warehouse layout is represented in PyBullet and Gazebo. Static coloured zones in Gazebo are visual markers and have no collision geometry. Waypoint offsets are relative to the first received robot pose and heading, so the default spawn yields targets (-1.3, -0.8), (1.3, -0.8), and home (0, -1.8). Reset restarts the mission using that same original frame.

The controller uses proportional heading correction and bounded forward speed. Obstacle handling stops translation and rotates toward a clearer side. Hysteresis avoids rapid switching at one threshold. This is a reactive local controller with timeouts, not SLAM or global navigation. It can oscillate or fail at dead ends; a timeout records failure instead of pretending success.

Time metrics and sensor freshness use local monotonic **wall time**, even with simulated sensors. They are not simulation-time metrics; paused operator time is excluded from the mission timeout. Inspection is a stationary dwell action, not camera-based defect detection. Path length is derived from odometry. No collision counter is implemented: laser proximity cannot prove zero collisions. Rotation clearance is a conservative heuristic rather than a full swept-volume guarantee.

Positive infinite laser ranges are interpreted as no return within range_max, a convention that must be checked against the deployed simulator. Relevant NaN/negative-infinite/too-large finite readings or missing required sectors invalidate the scan. Readings below range_min are treated as blocked. Freshness measures callback receipt time; this version does not reject delayed messages using their source stamps.

## Local reproduction

Run from the project root in a Python environment with NumPy, Matplotlib and PyBullet:

```bash
PYTHONPATH=ros2_ws/src/warehouse_inspector python -m unittest discover -s tests -v
PYTHONPATH=ros2_ws/src/warehouse_inspector python tests/offline_mission.py
```

The offline harness assumes a kinematic robot and synthetic clear scan. Its completion result is a controller sanity check only. It provides no evidence of ROS communication, physics, obstacle clearance or successful TheConstruct deployment.

## Report and academic use

Use REPORT_WORKSHEET.md to write from actual observations, then apply the Canvas assignment template. Confirm the reported AI-use allowance with the lecturer. This package is AI-assisted; do not claim it was created without assistance or fill in a personal reflection with events that did not happen. Review and understand each component before presenting it as assessed work. Include truthful acknowledgement under the module rules.

Submission still requires a 2700–3300-word report, Harvard references, public Colab link and accessible uniquely named Rosject. Confirm the Canvas submission time on 9 October 2026.
