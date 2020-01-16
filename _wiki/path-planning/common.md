---
layout: wiki
title: Common Knowledges on Path Planning
categories: [path-planning]
description: description of path planing
keywords: path-planing
---

# 知名研究机构
## University of Pennsylvania
- ### GRASP Lab,Vijay Kumar
  - #### Research Interests: planning, control, swarm
  - #### [HomePage]((www.kumarrobotics.org))

## Massachusetts Institute of Technology
- ### Jonathan How
  - #### Research Interests: modelling, control, planning
  - #### [HomePage](www.mit.edu/~jhow)
- ### Nicholas Roy
  - #### Research Interests: perception, learning
  - #### [HomePage](groups.csail.mit.edu/rrg)

## Carnegie Mellon University
- ### Nathan Michael
  - #### [HomePage](www.rislab.org)
- ### Sebastian Scherer
  - #### Research Interests: perception, planning
  - #### [HomePage](theairlab.org)

## University of California, Berkeley
- ### Markus Mueller
  - #### Research Interests: control, planning

## ETH Zurich
- ### ASL Team, Roland Siegwart
  - #### Research Interests: perception, control
  - #### [Homepage](asl.ethz.ch)
- ### Raffaello D’Andrea
  - #### Research Interests : control, swarm
  - #### [Homepage](raffaello.name)

## University of Zurich
- ### Davide Scaramuzza
  - #### Research Interests : perception, control
  - #### [Homepage](rpg.ifi.uzh.ch)

## Hong Kong University of Science Technology
- ### Shaojie Shen
  - #### Research Interests : UAV
  - #### [Homepage](uav.ust.hk)

- ### Ming Liu
  - #### Research Interests : UGV
  - #### [Homepage](ram-lab.com)

# 年轻学者
- ### Helen Oleynikova
  - #### [HomePage](helenol.github.io)

- ### Sikang Liu
  - #### [HomePage](github.com/sikang)

---

# 路径规划技术图谱
- ## 前端搜索
  - ### SEARCH-BASED PATH FINDING
    - Graph Search Basis
    - Dijkstra and A*
    - Jump Point Search
  - ### SAMPLING-BASED PATH FINDING
    - Probabilistic Road Map
    - Rapidly-exploring Random Tree (RRT)
    - Optimal Sampling-based Methods
    - Advanced Sampling-based Methods
  - ### KINODYNAMIC PATH FINDING
    - State-state Boundary Value Optimal Control Problem
    - State Lattice Search
    - Kinodynamic RRT*
    - Hybrid A*
- ## 后端优化
  - ### MINIMUM SNAP TRAJECTORY GENERATION
    - Differential Flatness
    - Minimum Snap Optimization
    - Closed-form Solution to Minimum Snap
    - Time Allocation
    - 
  - ### SOFT AND HARD CONSTRAINED TRAJECTORY OPTIMIZATION
    - Soft Constrained Trajectory Optimization
    - Hard Constrained Trajectory Optimization

---

# 地图
## 地图种类
- ### 占据栅格图(Occupancy grid map)
  - 最稠密
  - 结构化好
  - 直接索引
  - [源码链接](https://github.com/ANYbotics/grid_map)

- ### 八叉树地图(Octo-map)
  - 稀疏
  - 结构化好
  - 非直接索引

- ### 体素地图(Voxel hashing)
  - 最稀疏
  - 结构化好
  - 非直接索引
  - [源码链接](https://github.com/niessner/VoxelHashing)

- ### 点云地图(Point cloud map)
  - [PCL](http://pointclouds.org/)

- ### TSDF地图(Truncated Signed Distance Functions map)
  - [源码链接](https://github.com/personalrobotics/OpenChisel)

- ### ESDF地图(Euclidean Signed Distance Functions map)
  - [VoxBlox](https://github.com/ethz-asl/voxblox)
  - [FIESTA](https://github.com/HKUST-Aerial-Robotics/FIESTA)
  - [TRR’s Local Map](https://github.com/HKUST-Aerial-Robotics/Teach-Repeat-Replan)

- ### Free-space Roadmap
  - [源码链接](https://github.com/HKUST-Aerial-Robotics/Teach-Repeat-Replan)

- ### 维诺图(Voronoi Diagram map)
  - [源码链接](https://github.com/ethz-asl/mav_voxblox_planning)
