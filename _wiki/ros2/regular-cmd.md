---
layout: wiki
title: ROS2 Regular CMDs
categories: [Abstract]
description: ROS2 Regular CMDs
keywords: ros2, cmd
---

## 编译
- > colcon build 
- > colcon build --packages-select <package_name>

## 查看msg, service
- > ros2 interface show <package_name>/msg/<msg_name>
- > ros2 interface show <package_name>/srv/<srv_name>

## 运行节点
- > ros2 run <package_name> <node_name>

## 运行launch
- > ros2 run <package_name> <launch_file.py>
