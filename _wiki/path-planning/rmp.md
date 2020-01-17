---
layout: wiki
title: Probabilistic Road Map
categories: [path-planning]
description: description of RMP
keywords: RMP
---

# Probabilistic Road Map
- ## 介绍
  - 结构：学习+查询
  - 学习
    - 在状态空间内撒点
    - 删掉障碍物内的点
    - 针对每个点，将其与周围一定半径内的其他点连接起来
    - 若两点间的线段与障碍物发生碰撞，删除该连接
  - 查询
    - 利用Dijstra或者A*算法进行搜索
- ## 优劣势
  - 概率完备
  - 效率低

- ## 改进方式
  - Lazy collision-checking
    - 撒点和构建两点之间的连接时不检查是否与障碍物碰撞
    - 在路径已经搜索出来之后，查看当前路径有哪些路段发生了碰撞，删除碰撞路段
    - 重新搜索