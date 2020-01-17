---
layout: wiki
title: Rapidly-exploring Random Tree
categories: [path-planning]
description: description of RRT & RRT*
keywords: RRT, RRT*
---

# Rapidly-exploring Random Tree
- ## 介绍
  - 通过在状态空间内随机撒点，控制路径树的生长点和生长方向

- ## 算法流程
```
  输入：x_init, x_goal, Map
  Tree.init()
  for i = 1 to n
    x_rand = Sample(Map)
    x_near = Near(x_rand, Tree)
    x_new = Steer(x_near, x_rand, StepSize)
    e[i] = Edge(x_new, x_steer)
    if CollisionFree(Map, e[i])
      Tree.addNode(x_new)
      Tree.addEdge(e[i])
    if x_new == x_goal
      Success()
```

- ## 优劣势
  - 比RMP更加具有方向性
  - 非最优解
  - 效率低
  - 在整个空间中采样

- ## 改进方式
  - 用KDTree存储路径树中的点，加速查找x_near
  - Bidirectional RRT，从起点终点一起生长，直到两者相交

---

# RRT*

- ## 算法流程
```
  输入：x_init, x_goal, Map
  Tree.init()
  for i = 1 to n
    x_rand = Sample(Map)
    x_near = Near(x_rand, Tree)
    x_new = Steer(x_near, x_rand, StepSize)
    if CollisionFree(Map, e[i])
      xs_near = NearC(Tree, x_new)
      x_min = ChooseParent(xs_near)
      e[i] = Edge(x_new, x_min)
      Tree.addNode(x_new)
      Tree.addEdge(e[i])
      Tree.rewire()
    if x_new == x_goal
      Success()
```
- ## 细节展示
- ChooseParent，如图所示，x_new是由x_2生长出来的，但不是直接将x_2作为x_new的父，而是从一个固定的半径范围内选择到达x_new的总路程最短的节点作为父节点。

![ChooseParent](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/path-planning/rrt-choose-parent1.png)

![ChooseParent](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/path-planning/rrt-choose-parent2.png)

- Tree.rewire()

![Rewire](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/path-planning/rrt-rewire1.png)

![Rewire](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/path-planning/rrt-rewire2.png)