---
layout: wiki
title: ROS Service Client 
categories: [ROS]
description: ROS Service Client
keywords: ros, service client
---

### 在功能包的src文件夹中新建ABC.cpp文件
------
### 打开CMakeLists.txt文件，添加如下代码
- xxx_client是节点名，rosrun package/xxx_client
```
	add_executable(xxx_client src/ABC.cpp)   
	target_link_libraries(xxx_client ${catkin_LIBRARIES})
	add_dependencies(xxx_client package_name_gencpp)
```
------
### 把CMakLists.txt拉入QT
- 编写源代码，格式如下
```
	#include <ros/ros.h>
	#include <begin/my_srv.h>
	#include <cstdlib>
	#include<iomanip>

	int main(int argc, char **argv){
		ros::init(argc, argv, "client");
		ros::NodeHandle n;
		ros::ServiceClient client = n.serviceClient<begin::my_srv>("service");
		begin::my_srv srv;
		//srv包含了request和response两个成员
		//实际上可以定义为 begin::my_srv::Request req; begin::my_srv::Response rep;
		//对应的下面的调用是client.call(req,rep)
		srv.request.req = atoll(argv[1]);
		if(client.call(srv))
		{
			ROS_INFO_STREAM(srv.response.velocity.linear.x);
		}
		else
		{
			ROS_ERROR("Failed to judge!");
			return 1;
		}
	return 0;
	}
```
