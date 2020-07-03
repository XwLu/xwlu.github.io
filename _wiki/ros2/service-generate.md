---
layout: wiki
title: ROS2 Service Generate
categories: [ROS2]
description: ROS2 Service Generate
keywords: ros2, service
---
## 准备工作
```
cd dev_ws/src
ros2 pkg create --build-type ament_cmake tutorial_interfaces
cd tutorial_interfaces
mkdir srv
cd srv
vim AddThreeInts.srv
```

## 编写srv件
```
int64 a
int64 b
int64 c
\-\-\-
int64 sum
```

-----

## 修改Cmakelists文件 
```
cmake_minimum_required(VERSION 3.5)
project(tutorial_interfaces)

set(CMAKE_SKIP_BUILD_RPATH TRUE)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)
find_package(rosidl_default_generators REQUIRED)

rosidl_generate_interfaces(${PROJECT_NAME}
  "srv/AddThreeInts.srv"
)

ament_package()
```

------

## 修改packgae.xml文件
```
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  <name>tutorial_interfaces</name>
  <version>0.0.0</version>
  <description>TODO: Package description</description>
  <maintainer email="ifan.lyf@alibaba-inc.com">looyifan</maintainer>
  <license>TODO: License declaration</license>

  <buildtool_depend>ament_cmake</buildtool_depend>

  <build_depend>rosidl_default_generators</build_depend>
  <exec_depend>rosidl_default_runtime</exec_depend>
  <member_of_group>rosidl_interface_packages</member_of_group>

  <test_depend>ament_lint_auto</test_depend>
  <test_depend>ament_lint_common</test_depend>

  <export>
    <build_type>ament_cmake</build_type>
  </export>
</package>
```