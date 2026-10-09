# Report completion worksheet — not a final submission

Working title: **Semantic Warehouse Inspection: Scene Interpretation and Monitored Autonomous Missions**

Use the institution's template. Target 3000 words (permitted range 2700–3300). Verify what captions, tables and references count toward this total. This worksheet is an evidence guide, not text to present as your personal account.

## 1. Project definition — approximately 300 words

Explain the operational problem: a small mobile robot needs to visit inspection locations in a structured warehouse while obstacles constrain motion. Define the two tasks separately: Task 1 creates an interpretable scene; Task 2 executes and monitors a mission.

Measurable objectives: four custom object URDFs; at least eight fixed objects; a two-wheel robot; three viewpoints with moved robot; programmatic role query; two ROS2 sensor types; at least five states; human commands; complete mission with logs and screenshots.

Describe the difference between a semantic label assigned by the programmer and a role recognised by a perception model. The current design uses assigned labels, not learned recognition.

## 2. Research and design comparison — approximately 450 words

Consult the primary sources in SOURCES.md. Discuss the tradeoffs rather than merely listing tools. Why is DIRECT rendering useful in a notebook? Why does compatible sensor QoS matter? What does a reactive controller lose compared with map-based planning? What evidence would support deploying a similar robot outside simulation?

Add relevant academic research from your course library and read the full source before citing it. The verified sources supplied here are technical documentation, not a complete literature review.

## 3. Task 1 design and implementation — approximately 500 words

Describe the barrier, shelf, inspection-zone and landmark models and their roles. Nine fixed objects form two inspection locations and a home zone. The robot has two continuous wheel joints and a passive support sphere.

Explain the body-ID metadata dictionary and nearest-target distance calculation. Clarify that coordinates come from the simulator. Explain the three camera positions and why each reveals the scene differently. The robot is repositioned; it does not navigate dynamically in this task.

Evidence available: evidence/view_1.png, view_2.png, view_3.png, three_views.png and task1_execution.txt. Segmentation checks passed for visibility of all scene objects and the robot in all three views. Static positions remained unchanged. Local execution skipped the dependency installation cell; code cells ran directly in order under Python 3.11. A Colab run and public link are still required.

## 4. Task 2 design and implementation — approximately 800 words

Describe sensor input, mission state machine, velocity output, keyboard command input, status output and CSV writer. Draw the architecture from the implemented components. Trace a normal mission and an obstacle case through the state machine.

Explain relative waypoint coordinates, heading-error normalisation, speed bounds, front clearance threshold, hysteresis, timeouts and inspection dwell. Distinguish position estimates from true physical position. Explain why paused, completed and faulted states produce zero command.

Discuss receipt-time freshness and wall-clock metrics honestly. Do not claim simulation-time measurements or global planning. Explain the positive-infinity scan convention and conservative invalid-data handling. State that Gazebo markers have no collision geometry and the robot uses installed TurtleBot3 assets.

## 5. Execution and testing — approximately 550 words

Verified local results: 14 controller unit tests passed. The closed-loop kinematic harness completed its three-waypoint mission in 57.2 seconds of synthetic time with an odometry-derived path of approximately 5.215 m. These numbers belong to the offline harness, not to TheConstruct. The harness uses synthetic clear scans and ideal motion.

Complete this table only after running TheConstruct:

| Test | Initial condition | Expected behaviour | Actual result | Evidence |
|---|---|---|---|---|
| Normal mission | Default spawn and route | Inspect A, inspect B, return home | Not run | Pending |
| Obstacle ahead | Box on route | Stop, avoid or explicit failure | Not run | Pending |
| Pause/resume | Moving robot | Stop, then continue | Not run | Pending |
| Cancel | Active mission | IDLE and zero command | Not run | Pending |
| Sensor loss | Pause sensor-producing simulation | FAULT and zero command | Not run | Pending |
| Unreachable goal | Goal blocked | Timeout and explicit FAULT | Not run | Pending |

Record ROS distribution, simulator version, robot model, all parameters and start pose. Include failed tests and fixes. Do not equate a passing unit test with a passing integration test.

## 6. Evaluation and your reflection — approximately 400 words

Explain your own design decisions and what you learned from running and changing the system. Record only difficulties you actually encountered. A real development issue recorded during preparation was PyBullet installation failure under Python 3.12; installation and direct code-cell execution succeeded under Python 3.11. A local Jupyter kernel start also failed, so the cells were executed directly. These are development facts, not necessarily your personal experiences.

Evaluate reactive-controller limitations, simplified shapes, odometry drift, inspection simulated as dwell, camera occlusion and lack of contact evidence. Propose improvements and connect each to a measured limitation. Possible directions include global planning, source-stamp checks, richer perception and contact sensors, but do not say they were implemented.

## Before export

Insert student details yourself; do not invent them. Supply the actual public Colab URL and actual Rosject name. Add screenshot captions that name the environment. Complete the AI acknowledgement according to confirmed module rules. Check all citations, word count and access permissions. Export to PDF in the Canvas template only after the missing evidence is available.
