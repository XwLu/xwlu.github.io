---
layout: wiki
title: Jump Point Search
categories: [path-planning]
description: description of JPS
keywords: JPS
---

# JPS
- JPS的算法框架和A\*是一样的，但是节点的扩展规则不一样。A\*是无差别扩展每个节点的邻节点。而JPS是跳跃着扩展。
- JPS算法的节点扩展对象是force neighbor。
![Force Neighbor](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/path-planning/jps-force-neighbor.png)

## 流程演示
- 初始节点n_start压入open list
- 将n_start从open list中pop出来
- 从n_start斜对角搜索，找到一个关键节点n_1(它横向扩展到一个带有force neighbor的节点)，压入open list
- n_start压入closed list
![Expand1](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/path-planning/jps-expand1.png)
- 从open list中取出最优的(唯一的)n_1，横向搜索n_1，找到一个带有force neighbor的节点n_2，压入open list
![Expand2](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/path-planning/jps-expand2.png)
- 继续斜对角探索，没找到东西，n_1压入closed list
![Expand3](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/path-planning/jps-expand3.png)
- 从open list中取出最优的(唯一的)n_2，横向检查，撞墙
![Expand4](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/path-planning/jps-expand4.png)
- 从n_2斜向搜索，扩展到一个临近终点的force neighbor节点n_3，压入open list
![Expand5](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/path-planning/jps-expand5.png)
- n_2压入closed list
- 从open list中取出最优的(唯一的)n_3，横向检查，找到终点，搜索结束
![Expand6](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/path-planning/jps-expand6.png)

## 优缺点
- 大部分情况下效率比A*高很多
- 某些情况下效率不如A*
![bad-case](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/path-planning/jps-bad-case.png)
- 只能在uniform grid map中使用

