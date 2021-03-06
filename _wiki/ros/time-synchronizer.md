---
layout: wiki
title: ROS Time Synchronizer 
categories: [ROS]
description: ROS Time Synchronizer 
keywords: ros, time synchronizer
---

## 时间同步器
### 同步topic1和topic2发来的传感器数据
------
### 简单实现
```
	#include <message_filters/subscriber.h>
	#include <message_filters/time_synchronizer.h>
	#include <message_filters/sync_policies/approximate_time.h>
 
	using namespace sensor_msgs;
	using namespace message_filters;

	void callback(const sensor_msgs::NavSatFixConstPtr& fix, const tiggo_msgs::HeadingConstPtr& heading, const tiggo_msgs::ChassisInfoConstPtr& chassis)  //回调中包含多个消息
	{
  		// Solve all of perception here...
	}
 
	int main(int argc, char** argv)
	{
  		ros::init(argc, argv, "vision_node");
  		ros::NodeHandle nh;
  		typedef message_filters::sync_policies::ApproximateTime<sensor_msgs::NavSatFix, tiggo_msgs::Heading, tiggo_msgs::ChassisInfo> MySyncPolicy;
		message_filters::Subscriber<sensor_msgs::NavSatFix> sub_fix(pnh, "/strong/fix", 1);
		message_filters::Subscriber<tiggo_msgs::Heading> sub_heading(pnh, "/strong/heading", 1);
		message_filters::Subscriber<tiggo_msgs::ChassisInfo> sub_chassis(pnh, "/chassis_info", 1);
		message_filters::Synchronizer<MySyncPolicy> sync(MySyncPolicy(10), sub_fix, sub_heading, sub_chassis);
		sync.registerCallback(boost::bind(&callback, _1, _2, _3));
  		ros::spin();
  		return 0;
	}
```
### 类的实现
#### 头文件
```
	#include <message_filters/subscriber.h>
	#include <message_filters/synchronizer.h>
	#include <message_filters/sync_policies/approximate_time.h>
	
	class MySyn{
	public:
		MySyn();
		~MySyn();
		combineCallback(const nav_msgs::Odometry::ConstPtr& pOdom, const sensor_msgs::ImageConstPtr& pImg);
	private:
		ros::NodeHandle nh;
		typedef message_filters::sync_policies::ApproximateTime<sensor_msgs::NavSatFix, gl8_msgs::Heading> MySyncPolicy;
		message_filters::Subscriber<nav_msgs::Odometry>* odom_sub_ ;
		message_filters::Subscriber<sensor_msgs::Image>* img_sub_;
		message_filters::Synchronizer<slamSyncPolicy>* sync_;
		
	}
```
#### 类cpp文件
```
	MySyn::MySyn(){
		odom_sub_ = new message_filters::Subscriber<nav_msgs::Odometry>(ar_handle, "/odom", 1);
		img_sub_  = new message_filters::Subscriber<sensor_msgs::Image>(ar_handle, "/usb_cam/image_raw", 1);
		sync_ = new message_filters::Synchronizer<MySyncPolicy>(slamSyncPolicy(10), *odom_sub_, *img_sub_);//这里的10就是误差容忍数
		sync_->registerCallback(boost::bind(&MySyn::combineCallback,this, _1, _2));

		ros::spin();
	}
	MySyn::~MySyn(){}
	
	MySyn::combineCallback(const nav_msgs::Odometry::ConstPtr& pOdom, const sensor_msgs::ImageConstPtr& pImg){
		//TODO:
	}
```
#### main.cpp
```
	int main(int argc, char **argv)
	{
  		ros::init(argc, argv, "node_name");
		MySyn my_syn;
		return 0;
	}
```
