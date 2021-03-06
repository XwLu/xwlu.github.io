---
layout: wiki
title: ROS Action Client 
categories: [ROS]
description: ROS Action Client
keywords: ros, action client
---

### C++实现:
```
	#include <chores/DoDishesAction.h>
	#include <actionlib/client/simple_action_client.h>
	
	typedef actionlib::SimpleActionClient<chores::DoDishesAction> Client;
	
	int main(int argc, char** argv)
	{
		ros::init(argc, argv, "do_dishes_client");
		Client client("do_dishes", true); // true -> don't need ros::spin()
		client.waitForServer();
		chores::DoDishesGoal goal;
		// Fill in goal here
		client.sendGoal(goal);
		client.waitForResult(ros::Duration(5.0));
		if (client.getState() == actionlib::SimpleClientGoalState::SUCCEEDED)
			printf("Yay! The dishes are now clean");
		printf("Current State: %s\n", client.getState().toString().c_str());
		return 0;
	}
```
### Note: For the C++ SimpleActionClient, the waitForServer method will only work if a separate thread is servicing the client's callback queue. This requires passing in true for the spin_thread option of the client's constructor, running with a multi-threaded spinner, or using your own thread to service ROS callback queues.
------

### Python:
```
	#! /usr/bin/env python
	
	import roslib
	roslib.load_manifest('my_pkg_name')
	import rospy
	import actionlib
	
	from chores.msg import DoDishesAction, DoDishesGoal

	if __name__ == '__main__':
		rospy.init_node('do_dishes_client')
		client = actionlib.SimpleActionClient('do_dishes', DoDishesAction)
		client.wait_for_server()

		goal = DoDishesGoal()
		# Fill in the goal here
		client.send_goal(goal)
		client.wait_for_result(rospy.Duration.from_sec(5.0))
```
