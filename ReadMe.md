# Autonomous Vehicle Vision System and Navigation Stack Project

## Project Overview

This project aims to build a Tesla-like vision system and navigation stack for maneuvering a car in a simulator and later deploying the same system in an autonomous vehicle. The primary tools include the CARLA Simulator, ROS for communication and other functionalities, and a computer vision pipeline for scene understanding.

## Prerequisites

- System Requirements:
  - OS: Ubuntu 22.04
  - GPU: NVIDIA (6+ GB VRAM)
  - Memory: 40GB
- Install Python 3.10, we will download the setup files of CARLA to have support for 3.10
- Install Nvidia Drivers, Nvidia CUDA ToolKit.
- Setup CARLA 0.9.14 with GPU support.
  - Download CARLA [0.9.14](https://carla-releases.s3.us-east-005.backblazeb2.com/Linux/CARLA_0.9.14.tar.gz)
  - Extract CARLA in a directory

    ```bash
    tar -xvzf CARLA_0.9.14.tar.gz -C carla-simulator
    ```

- Install ROS2 (Humble)
  - Prepare system

    ```bash
    sudo apt install software-properties-common
    sudo add-apt-repository universe
    sudo apt update && sudo apt install curl -y
    sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
    sudo apt update && sudo apt upgrade -y
    sudo apt-get install --only-upgrade udev systemd
    ```

  - Install and Setup ROS2-Humble
  
    ```bash
    sudo apt install ros-humble-desktop
    sudo apt install python3-colcon-common-extensions
    echo "source /usr/share/colcon_argcomplete/hook/colcon-argcomplete.zsh" >> ~/.zshrc
    echo "source /usr/share/colcon_cd/function/colcon_cd.sh" >> ~/.zshrc
    echo "export _colcon_cd_root=/opt/ros/humble/" >> ~/.zshrc
    source ~/.zshrc
    sudo apt-get install python3-rosdep
    sudo rosdep init
    rosdep update
    ```

- Setup Carla-Ros-Bridge for Humble
  - Setup carla-ros-bridge
  
    ```bash
    mkdir -p ~/carla-ros-bridge/catkin_ws/src
    cd carla-ros-bridge/catkin_ws/src
    git clone --recurse-submodules https://github.com/gezp/carla_ros.git -b humble-carla-0.9.14
    ```

  - Install the package

    ```bash
    cd carla-ros-bridge/catkin_ws
    rosdep install --from-paths src --ignore-src -r
    colcon build --symlink-install
    source install/setup.bash
    ```

- Run demo for testing
  - Launch CARLA Simulator with GPU support
  
    ```bash
    ./CarlaUE4.sh -prefernvidia
    ```
  
  - Launch the Ego Vehicle
  
    ```bash
    cd carla-simulator
    ./CarlaUE4.sh -prefernvidia
    ```
  
  - Launch the Ego Vehicle
  
    ```bash
    ros2 launch carla_ros_bridge carla_ros_bridge_with_example_ego_vehicle.launch.py
    ```
  
  - Change and load the environment, change the `TownXX` part
  
    ```bash
    ros2 launch carla_ros_bridge carla_ros_bridge.launch.py synchronous_mode:=True town:=Town03 timeout:=200
    ```
  
  - Use the provided `objects.json` and `carla_ros.rviz` to spawn the objects and see the output from the Ego Vehicles perspective.
  
    ```bash
    ros2 launch carla_ros_bridge ros2 launch carla_spawn_objects carla_example_ego_vehicle.launch.py spawn_sensors_only:=False objects_definition_file:=objects.json
    rviz2 -d carla_ros.rviz
    ```
