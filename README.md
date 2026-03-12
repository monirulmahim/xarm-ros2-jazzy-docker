# xArm7 ROS2 Docker Setup Installation Guide (ROS Jazzy)

This guide explains how to run **xArm ROS2 with ROS Jazzy inside Docker on macOS**, build the workspace, and control the robot.

---

# 1. Install Docker (macOS)

Download and install Docker Desktop from the official site:

https://www.docker.com/products/docker-desktop/

After installation, verify Docker is working:

```bash
docker --version
```

---

# 2. Pull ROS Jazzy Docker Image

```bash
docker pull ros:jazzy
```

---

# 3. Start the Container

Create and start a container:

```bash
docker run -it --name ros_jazzy_xarm ros:jazzy
```

---

# 4. Install Required Dependencies

Inside the container:

```bash
apt update
apt install -y \
git \
python3-colcon-common-extensions \
python3-rosdep \
python3-vcstool \
python3-pip
```

---

# 5. Create ROS2 Workspace

```bash
mkdir -p /root/xarm_ws/src
cd /root/xarm_ws/src
```

Clone the xArm ROS2 repository:

```bash
git clone -b jazzy https://github.com/xArm-Developer/xarm_ros2.git
```

---

# 6. Build the Workspace

```bash
cd /root/xarm_ws

source /opt/ros/jazzy/setup.bash

apt update
rosdep update

rosdep install --from-paths src --ignore-src -r -y

colcon build --symlink-install

source install/setup.bash
```

---

# 7. Update Submodules

```bash
cd /root/xarm_ws/src/xarm_ros2

git submodule sync
git submodule update --init --remote
```

---

# 8. Rebuild Workspace

```bash
cd /root/xarm_ws

source /opt/ros/jazzy/setup.bash

rosdep update

rosdep install --from-paths src --ignore-src -r -y

colcon build --symlink-install

source install/setup.bash
```

---

# 9. Load ROS Environment

Each new terminal session requires sourcing:

```bash
source /opt/ros/jazzy/setup.bash
source /root/xarm_ws/install/setup.bash
```

---

# 10. Launch the xArm Driver

Replace the IP with your robot's IP address.

```bash
ros2 launch xarm_api xarm6_driver.launch.py robot_ip:=192.168.1.117
```

---

# 11. Enable Robot Motion

```bash
ros2 service call /xarm/motion_enable \
xarm_msgs/srv/SetInt16ById \
"{id: 8, data: 1}"
```

---

# 12. Test Robot Movement

```bash
ros2 service call /xarm/set_position \
xarm_msgs/srv/MoveCartesian \
"{pose: [300, 0, 250, 3.14, 0, 0], speed: 50, acc: 500, mvtime: 0}"
```

---

# Restarting the Docker Container

## 1. Check existing containers

```bash
docker ps -a
```

## 2. Start a shell in the container

First start the container:
```bash
docker start ros_jazzy_xarm
```
Then open an interactive shell inside the container:
```bash
docker exec -it ros_jazzy_xarm bash
```

## 3. Verify workspace

```bash
cd /root/xarm_ws
ls
```

## 4. Source ROS

```bash
source /opt/ros/jazzy/setup.bash
source /root/xarm_ws/install/setup.bash
```

## 5. Launch driver again

```bash
ros2 launch xarm_api xarm6_driver.launch.py robot_ip:=192.168.1.117
```

## 6. Enable motion

```bash
ros2 service call /xarm/motion_enable \
xarm_msgs/srv/SetInt16ById \
"{id: 8, data: 1}"
```

## 7. Test movement

```bash
ros2 service call /xarm/set_position \
xarm_msgs/srv/MoveCartesian \
"{pose: [300, 0, 250, 3.14, 0, 0], speed: 50, acc: 500, mvtime: 0}"
```

---

# Connecting Docker to VS Code

1. Install the **Dev Containers** extension in VS Code.

2. Open the command palette:

```
Cmd + Shift + P
```

3. Select:

```
Dev Containers: Attach to Running Container
```

4. Choose:

```
ros_jazzy_xarm
```

You are now working inside the Docker container using VS Code.

---

# Installing Python 3.11 in the Container

If your project requires a specific Python version:

```bash
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt update
sudo apt install python3.11 python3.11-venv python3.11-dev python3.11-pip
```

Verify installation:

```bash
python3.11 --version
```

---

# Notes

* Make sure the **robot and computer are on the same network**.
* Update the **robot IP address** accordingly.
* Always **source ROS environments** when opening a new terminal.
* Tested on **macOS Tahoe** running on **Apple Silicon (M4 Pro).**

---
