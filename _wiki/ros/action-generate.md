---
layout: wiki
title: ROS Action Gneerate 
categories: [ROS]
description: ROS Action Generate
keywords: ros, action generate
---

### 新建xxx.action文件
```
	# Define the goal
	uint32 dishwasher_id  # Specify which dishwasher we want to use
	---
	# Define the result
	uint32 total_dishes_cleaned
	---
	# Define a feedback message
	float32 percent_complete
```
------
### 将如下内容添加到CmakeLists.txt文件
```
	find_package(catkin REQUIRED genmsg actionlib_msgs actionlib)
	add_action_files(DIRECTORY action FILES xxx.action)
	generate_messages(DEPENDENCIES actionlib_msgs)
	catkin_package(CATKIN_DEPENDS actionlib_msgs)

	add_executable(fibonacci_server src/fibonacci_server.cpp)

	target_link_libraries(
		fibonacci_server
		${catkin_LIBRARIES}
	)

	add_dependencies(
		fibonacci_server
		${actionlib_tutorials_EXPORTED_TARGETS}
	)
```
------
### 在package.xml文件中添加如下：
```
	<build_depend>actionlib</build_depend>
	<build_depend>actionlib_msgs</build_depend>
	<run_depend>actionlib</run_depend>
	<run_depend>actionlib_msgs</run_depend>
```
