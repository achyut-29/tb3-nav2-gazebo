# TurtleBot3 + Nav2 in Gazebo

Autonomous navigation with a TurtleBot3 (waffle) in the `turtlebot3_world` Gazebo environment, using the standard ROS 2 Navigation2 stack. No custom navigation code, only existing packages and launch files.

**Tested on:** Ubuntu 24.04, ROS 2 Jazzy, Gazebo (gz sim)

## Install

Add the ROS 2 apt repository first (see the official ROS 2 Jazzy install guide), then:

```bash
sudo apt install -y ros-jazzy-desktop ros-dev-tools \
  ros-jazzy-navigation2 ros-jazzy-nav2-bringup \
  ros-jazzy-turtlebot3 ros-jazzy-turtlebot3-msgs \
  ros-jazzy-turtlebot3-gazebo ros-jazzy-turtlebot3-navigation2 \
  ros-jazzy-ros-gz ros-jazzy-ros-gz-sim ros-jazzy-ros-gz-bridge

echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc
echo "export TURTLEBOT3_MODEL=waffle" >> ~/.bashrc
source ~/.bashrc
```

## Run

Use three terminals.

**1. Gazebo world**
```bash
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py
```

**2. Nav2 + RViz**
```bash
ros2 launch turtlebot3_navigation2 navigation2.launch.py use_sim_time:=True
```

**3. Set the initial pose** (robot spawns at x=-2.0, y=-0.5 in the map frame)
```bash
./scripts/set_initial_pose.sh
```

Wait a few seconds until the Navigation 2 panel in RViz shows Navigation and Localization as active and the laser scan lines up with the map walls. Then click **Nav2 Goal** in RViz, click a free spot on the map, drag to set the heading, and the robot plans a path and drives to the goal while avoiding the obstacles.

## Notes

- Clicking **2D Pose Estimate** in the wrong place makes AMCL start from a wrong pose and the costmaps smear. The script above sets the exact spawn pose instead.
- If the costmaps look stale after a bad pose, clear them:
  ```bash
  ros2 service call /local_costmap/clear_entirely_local_costmap nav2_msgs/srv/ClearEntireCostmap "{}"
  ros2 service call /global_costmap/clear_entirely_global_costmap nav2_msgs/srv/ClearEntireCostmap "{}"
  ```
- If Gazebo runs very slowly, check `glxinfo -B | grep renderer`. `llvmpipe` means rendering is on the CPU; a working GPU driver fixes it.

## Demo

Screen recording (about 1m45s, Gazebo and RViz side by side): [demo/nav2_demo.webm](demo/nav2_demo.webm). Download it to play.
