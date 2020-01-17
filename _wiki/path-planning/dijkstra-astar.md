---
layout: wiki
title: Dijkstra and A*
categories: [path-planning]
description: description of Dijkstra and A*
keywords: Dijkstra, A*
---

### [可视化链接](http://qiao.github.io/PathFinding.js/visual/)

# Dijkstra
- 优势：完备性、最优性得到保证
- 劣势：节点扩展时没有方向的引导

# A*
- 增加了启发值的Dijkstra
- 启发值H(n)一般是指当前节点n到终点的距离(曼哈顿距离、欧氏距离)，**H(n)一定要比真实的n到终点距离小(admissible)**，否则A*算法不是最优解
- 曼哈顿距离不一定是admissible、欧氏距离一定是admissible

# Weighted A*
- f = g + w×h，w>1。
- 通过调节w，可以让搜索时间加快，但是最优性不能得到保证

# 启发函数的设计技巧
- 要尽可能接近当前节点n到终点的真实距离(tight)
  - 在栅格环境下，可斜对角运动时，Diagonal Heuristic > Euclidean Heuristic > Manhattan Heuristic
- Tie Breaker
  - 很多节点的f是一样的，增加了搜索量。通过修改h，可以让他们有细微的差别：h = h*(1.0+p)