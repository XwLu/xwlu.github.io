---
layout: wiki
title: ROS Publisher
categories: [ROS]
description: ROS Publisher
keywords: ros, publisher
---

### 新建包(后面跟着的是依赖)当然，也可以在已有的包里写，一个包含有多个节点
> catkin_create_pkg listen rospy roscpp std_msgs geometry_msgs sensor_msgs turtlesim
----- 
### 新建cpp文件
> gedit xx.cpp
-----
### 修改Cmakelists文件 
- find_package(catkin REQUIRED COMPONENTS)里面的依赖
- 添加可执行文件声明
```
	add_executable(ABC src/xxx.cpp)  #rosrun package_name ABC，文件路径要对
	target_link_libraries(ABC ${catkin_LIBRARIES})

	include_directories(
		include
		${catkin_INCLUDE_DIRS}
	)
```
------
### 修改packgae.xml文件
```
	<build_depend>xxx</build_depend>
	<run_depend>xxx</run_depend>
```
------
### 编写源码（格式如下）
```
	#include <ros/ros.h>
	#include <std_msgs/String.h>
	#include <begin/Vel.h>
	#include <geometry_msgs/Twist.h>
	#include <sstream>
	#include <iomanip>

	int main(int argc, char ** argv){
		ros::init(argc, argv, "give_out");//节点的名字
		ros::NodeHandle n;
		ros::Publisher my_pub = n.advertise<begin::Vel> ("my_Vel", 1000);
						//my_Vel是话题的名字
		ros::Rate loop_rate(1);
		int cnt = 1;
		begin::Vel msg;  //定义一个Vel类型的数据
		while(ros::ok())
		{
			if((cnt%2) == 0) msg.linear.x = 1.0;
			else msg.linear.x = 2.0;
			ROS_INFO_STREAM(std::setprecision(2) << std::fixed<< "velocity  = " << msg.linear.x );
			my_pub.publish(msg);//发布消息到话题
			loop_rate.sleep();//这边的loop_rate要和上面的一致。
			cnt = cnt * 3+ 1;
		}
	return 0;
	}
```
