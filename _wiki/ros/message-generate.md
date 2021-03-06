---
layout: wiki
title: ROS Message Generate
categories: [ROS]
description: ROS Message Generate
keywords: ros, message generate
---

## 注意点
- 创建了自己的msg之后,第一次执行编译会提示No such file or directory
- 解决方案是在add_executable()后面加上：
> add_dependencies(source_file_name package_name_generate_messages_cpp)
- 或者更通用的：
> add_dependencies(${PROJECT_NAME}_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

### 创建功能包
> catkin_create_pkg xxx std_msgs roscpp rospy
------
### 创建msg文件夹
> cd xxx
> mkdir msg
------
### 新建msg文件
> gedit ABC.msg
#### 内容的话就定义你需要的数据
```
	//这里面有个结构体的技巧，可以先定义一个Vector.msg，内容如下
	float64 x
	float64 y
	float64 z
	//这个的意思就是说Vector现在是个结构体，包含x，y，z三个变量
	//再定义Velocity.msg，内容如下
	Vector linear
	Vector angular
	//这个的意思就是说Velocity是个结构体包含了两个vector类型的数据
	//这实现了结构体的嵌套
```
------
### 修改packgae.xml文件，添加如下两句
```
	<build_depend>message_generation</build_depend>
	<run_depend>message_runtime</run_depend>
```
------
### 修改Cmakelists文件 
```
	find_package(catkin REQUIRED COMPONENTS
		roscpp
		rospy
		std_msgs
		message_generation)
```
- 这边最重要的是添加message_generation
- 找到catkin_package(...)，添加 CATKIN_DEPENDS roscpp rospy std_msgs message_runtime//这一步好像可以省去，不加也没关系
- 找到如下代码，并修改，有多少msg文件就添加多少
```
	add_message_files(
	FILES
	ABC.msg
	)
```
- 找到如下代码，解开注释
```
	generate_messages(
	DEPENDENCIES
	std_msgs
	)
```
------
### 重新编译，并检查
> rosmsg show package_name/msg_name
- 输出正常就表示创建成功

