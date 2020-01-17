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

- 流程演示
  - 初始节点N_start压入队列PQ
  - 从N_start斜对角搜索，找到一个关键节点(它横向扩展到一个force neighbor)，