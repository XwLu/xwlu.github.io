---
layout: wiki
title: ROS Subscirber
categories: [ROS]
description: ROS Subscriber
keywords: ros, subscriber
---

### 注意：如果不知道topic的名字，可以用rostopic list查看
------
### 新建cpp文件
> gedit xx.cpp
------
### 修改Cmakelists文件 
- find_package(catkin REQUIRED COMPONENTS)里面的依赖
- 添加可执行文件声明
```
	add_executable(ABC src/xxx.cpp)  //rosrun package_name ABC，文件路径要对
	target_link_libraries(ABC ${catkin_LIBRARIES})
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
	#include <geometry_msgs/Twist.h>
	#include<iomanip>

	void callback(const geometry_msgs::Twist & msg)//回调函数名字随意
	{
		ROS_INFO_STREAM(std::setprecision(2) << std::fixed << "velocity  = " << msg.linear.x << "," << msg.angular.z );
	}

	int main (int argc, char  ** argv){
		ros::init(argc, argv, "listen");//节点的名字
		ros::NodeHandle nh;//将节点交给master

		ros::Subscriber sub = nh.subscribe ( "turtle1/cmd_vel", 1000, &callback);//和上面一致

		ros::spin();//关于ros::spin和ros::spinOnce的区别，看浅谈70页
	}
```
