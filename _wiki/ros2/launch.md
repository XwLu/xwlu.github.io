---
layout: wiki
title: ROS2 Launch
categories: [ROS2]
description: ROS2 Launch
keywords: ros2, launch
---

## 创建launch文件
```
cd <package_name>
mkdir launch
touch launch/<launch_file_name>.py
```

## 编写launch文件
```
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    return LaunchDescription([
        Node(
            package='turtlesim',
            namespace='turtlesim1',
            executable='turtlesim_node',
            name='sim'
        ),
        Node(
            package='turtlesim',
            namespace='turtlesim2',
            executable='turtlesim_node',
            name='sim'
        ),
        Node(
            package='turtlesim',
            executable='mimic',
            name='mimic',
            remappings=[
                ('/input/pose', '/turtlesim1/turtle1/pose'),
                ('/output/cmd_vel', '/turtlesim2/turtle1/cmd_vel'),
            ]
        )
    ])
```

-----

## 运行launch文件
```
ros2 launch <package_name> <launch_file_name>
```