---
layout: wiki
title: ROS Launch 
categories: [ROS]
description: ROS Launch
keywords: ros, launch
---

### 格式:
```
	<launch>
		<include
		file = "$(find packge-name)/launch-name.launch"
		/>

		<arg
			name = "arg-name"
			default = "x";
		/>

		<node
			pkg = "package-name"
			type = "executable-name"
			name = "node-name"
			ouput = "screen"
			respawn = "true"
			required = "true"
			launch-prefix = "xterm -e"
			ns = "namespace"
		>

		<remap from = "origin" to "new"/>
		<param name = "param-name" value = "param-value"/>

		</node> 

		<group ns = "namespace" if = "$(arg arg-name)"/>
			<param name = "param-name" value = "param-value"/>
		</group>
	    
	</launch>
```
------
- name:这边的node-name会覆盖所有的ros::init定义的名字
- 如果要使用一个匿名名称，可以用name="$(anon base_name)"
- 注意，对同一个基本名的重复利用会产生相同的匿名名称，所以每一个匿名启动节点都得有不同的基本名
- output:节点会把标准输出打印在显示器上，而不是log文件
- respawn:节点停止后自动重启
- required:必要节点，终止的时候其他节点会被杀掉。
- launch-prefix="xterm -e"为该节点单独开一个终端
- 启动前缀属性不止xterm还有其他，比如降低进程优先级
- ns:命名空间，在不同命名空间内可以启动相同的节点，比如两只乌龟，两个遥控节点。
- remap:话题的重映射中使用的一般是相对名称，在应用所有重映射之前，所有名称都要先解析为全局名称。浅谈上反转乌龟速度的（在launch一文中的）好例子
- include:可以包含其他启动文件
- arg:关于启动参数，浅谈P122
- group有两个好处：1.统一group内的节点命名空间 2.通过if判断0或1决定是否启动group内节点
- param设置参数，如果是在节点元素中，则是私有名称
