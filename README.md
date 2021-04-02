# robot_instructions
## Instructions Catalog, and Reference Guide

### Overview
Catalog of instructions, and references to relevant repositories for robotic mobile platforms, sensors, and systems.  The repositories and instruction sets contained in this repository, or referenced herein, often overlap.  For example, the SARA sensor platform may be used with a number of the mobile platforms found below, and may require slightly different modifications made to any one of those platforms.  Those modifications will only be found in the sara_sensors_pkgs repository, not in, for example, usma_jackal.  Similarly, there is a reference to Google Cartographer, which is a Simultaneous Localization and Mapping (SLAM) algorithm, but there may be sensor or platform specific instructions or packages that help to get this algorithm working using ROS on a given platform with a specific sensor, or set of sensors.  This catalog attempts to provide information and resources that do not conflict with each other, or confuse users.  Please contact any of the contributors to this repository with questions.

### 1. Mobile Platforms
- [Clearpath Jackal](https://github.com/westpoint-robotics/usma_jackal)
- [Clearpath Husky](https://github.com/westpoint-robotics/husky_rrc)
- This is the main repository for [GVR-Bot](https://github.com/westpoint-robotics/usma_gvrbot).  Within that, a good place to start is by installing the dependency list in the README, cloning the repository to your working catkin workspace.  Next, you will want to verify that you can communicate with the GVR-Bot through a Linux computer (or VM), and control the motors.  For that, follow the [Instructions for connecting Linux computers to the GVR-Bot
](https://github.com/westpoint-robotics/usma_gvrbot/blob/master/linux_connect.md).  If you are using a more recent version of Ubuntu than is listed, make the substitution as needed (e.g. melodic in place of indigo).

### 2. Sensors
- [FLIR cameras](https://github.com/westpoint-robotics/usma_spinnaker)
- [microstrain IMU](https://github.com/ros-drivers/microstrain_mips)
- [Gobi thermal camera](https://github.com/westpoint-robotics/Xenics-Gobi-ROS_Capture)
- [LiDAR](https://github.com/westpoint-robotics/usma_LiDAR)
- [Intel RealSense cameras](https://github.com/IntelRealSense/realsense-ros) and [drivers](https://github.com/IntelRealSense/librealsense)

### 3. Algorithms
- [Google Cartographer](https://google-cartographer-ros.readthedocs.io/en/latest/)
- [Motion Capture](https://github.com/westpoint-robotics/usma_optitrack)

### 4. System Integration
#### a. Sensor Platforms
- [SARA](https://github.com/westpoint-robotics/sara_sensors_pkgs): The ARL SARA sensors frame with LiDAR, Depth Camera, Microstrain IMU.
#### b. Infrastructure
- [Networking](https://github.com/westpoint-robotics/robot_instructions/tree/main/networking): Computer to computer, or sensor to computer ethernet networks.
- [Time synchronization](https://github.com/westpoint-robotics/robot_instructions/tree/main/time_synchronization): NTP computer to computer time synchronization.

### TODO:
- Integrate [ATAK Bridge](https://github.com/westpoint-robotics/atak_bridge) somewhere.
- Improve organizational categories.
- Expand relevant repositories and instruction sets.
- What to do with things like Cartographer, where there may be better instructions for a specific platform (e.g. Jackal), or sensor (depth cam vs. LiDAR)?
- Some systems (e.g. GVR-Bot) have multiple relevant repositories and or instruction sets.  These may be context specific (simulation versus the physical robot), or may be competing versions.  Determine which is which, and organize using sub menus, or similar.
