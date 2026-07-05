# project-3-fleet_management-
This is a pure-Python **ROS 2 Humble** package (no Gazebo/URDF — just simulated pub/sub logic) for a warehouse multi-robot fleet manager. Here are the run commands.

## Setup

```bash
source /opt/ros/humble/setup.bash
sudo apt install ros-humble-rclpy ros-humble-geometry-msgs
```

## Build

```bash
cd warehouse_fleet_ws
colcon build --packages-select warehouse_fleet
source install/setup.bash
```

## Run — Option A: one-shot launch file (recommended)

```bash
ros2 launch warehouse_fleet warehouse_fleet.launch.py
ros2 launch warehouse_fleet warehouse_fleet.launch.py num_robots:=5   # more robots
```

## Run — Option B: quick_start.sh (builds + auto-launches in tmux panes)

```bash
cd warehouse_fleet_ws/src/warehouse_fleet
chmod +x quick_start.sh
bash quick_start.sh          # defaults to 3 robots
bash quick_start.sh 5        # 5 robots
```
If `tmux` isn't installed it just falls back to the launch file above.

## Run — Option C: each node in its own terminal (good for learning)

```bash
# Terminal 1 — Fleet Manager
ros2 run warehouse_fleet fleet_manager.py

# Terminal 2 — Robot 1
ros2 run warehouse_fleet robot_controller.py --ros-args -p robot_name:=robot_1 -p start_x:=0.0 -p start_y:=0.0

# Terminal 3 — Robot 2
ros2 run warehouse_fleet robot_controller.py --ros-args -p robot_name:=robot_2 -p start_x:=1.0 -p start_y:=0.0

# Terminal 4 — Robot 3
ros2 run warehouse_fleet robot_controller.py --ros-args -p robot_name:=robot_3 -p start_x:=2.0 -p start_y:=0.0

# Terminal 5 — Live dashboard
ros2 run warehouse_fleet warehouse_monitor.py

# Terminal 6 — Interactive task CLI (optional)
ros2 run warehouse_fleet task_dispatcher.py
```

## Interacting at runtime

Inject a task via the CLI dispatcher:
```
fleet> add 3.0 2.0 "Pick item from row 3"
fleet> list
fleet> status
fleet> help
```

Or directly over a topic:
```bash
ros2 topic pub --once /add_task std_msgs/String "data: '{\"x\": 5.0, \"y\": 4.0, \"label\": \"Drop-off zone\"}'"
```

Monitoring:
```bash
ros2 topic echo /fleet_status
ros2 topic echo /robot_1/pose
rqt_graph
```

Note: since there's no Gazebo/URDF here, this only needs ROS 2 Humble (rclpy) installed — no simulator or GPU required. It won't run in this sandbox since ROS 2 isn't installed here, but it's lightweight enough to run on a modest Ubuntu 22.04 machine or VM.
