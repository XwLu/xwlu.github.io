---
layout: wiki
title: ROS2 Create Workspace & Package
categories: [ROS2]
description: ROS2 create workspace & package
keywords: ros2, workspace, package
---

## 创建workspace & ros-package
- ### macos
  ```
  # source ros env
  . ~/ros2_install/ros2-osx/setup.zsh

  # create dir
  mkdir -p ~/dev_ws/s
  cd ~/dev_ws/src

  # create a pkg in src
  ros2 pkg create --build-type ament_cmake <package_name> --dependencies <dep1> <dep2>

  # build
  cd ~/dev_ws
  colcon build
  ```

## source ws
- ### macos
  ```
  cd ~/dev_ws
  . install/setup.zsh
  ```

## run package
- ### macos
  ```
  ros2 run <package_name> <node_name>
  ```