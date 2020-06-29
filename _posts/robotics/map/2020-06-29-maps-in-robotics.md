---
layout: post
title: Maps in Robotics
categories: [Robotics]
description: 记录机器人学中优秀的地图管理项目
keywords: map, robotics
---

## 前言
- 地图是路径规划中最为重要的因素之一，不同的地图可以适配不同的路径规划算法生成效果不同的路径

---

## 优秀的地图管理项目
- ### [Anybotics Grid Map](https://github.com/ANYbotics/grid_map)
  - 这是一个具有ROS接口的C++库，用于管理具有多个数据层的二维网格地图。它是为移动机器人地图设计的，可以存储海拔、方差、颜色、摩擦系数、落脚点质量、表面法向、可通过性等数据。它被用于设计用于崎岖地形导航的[Robot-Centric Elevation Mapping](https://github.com/anybotics/elevation_mapping)项目中。

- ### [OctoMap](https://octomap.github.io/)
  - OctoMap库实现了一种3D占据栅格方法（基于C++）。map实现基于八叉树，旨在满足以下要求：
    - **全三维模型**。地图能够模拟任意环境，而无需事先假设。
    - **可更新**。可以随时以概率的方式添加新信息或传感器读数。多个机器人能够为同一张地图做出贡献。当探索新的区域时，先前记录的地图是可扩展的。
    - **灵活**。地图的范围不用预定义，地图会根据需要动态展开。地图是多分辨率的，全局规划可以使用粗略的地图，而局部规划可以使用精细的分辨率进行操作。这也允许有效的可视化，从粗略到详细的特写视图。
    - **紧凑**。地图被有效地存储在内存和磁盘上。即使在带宽限制下，也可以生成压缩文件供以后使用或在机器人之间方便地交换。
