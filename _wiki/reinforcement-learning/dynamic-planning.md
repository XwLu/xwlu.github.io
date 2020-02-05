---
layout: wiki
title: Markov Decision Processes
categories: [reinforcement-learning]
description: wiki on Reinforcement Learning
keywords: reinforcement-learning, MDP
---

# 概念定义
- 当有一个精确的环境模型时,可以用动态规划去解
- 将问题分解成子问题,通过解决子问题,来解决原问题
- 贝尔曼方程是关键

# 问题特性
- ## 最优子结构
  - 满足最优性原理：不论初始状态和初始决策如何,对于前面决策所造成的某一状态而言,其后各阶段的决策序列必须构成最优策略
  - 最优的解可以被分解成子问题的最优解
- ## 交叠式子问题
  - 子问题能够被多次重复
  - 子问题的解要能够被缓存并再利用

# 策略评价
- ## 问题
> 给定一个策略<img src="https://latex.codecogs.com/gif.latex?\pi "/>,求对应的值函数<img src="https://latex.codecogs.com/gif.latex?v_{\pi }(s)"/>或者<img src="https://latex.codecogs.com/gif.latex?q_{\pi }(s,a)"/>
- ## 方法
  - 直接解
    - 可以直接求得精确解
    - 时间复杂度
  - 迭代解
    - 利用贝尔曼期望方程迭代求解
    - 可以收敛到精确解

# 策略提升
  - 根据现有的策略评价结果<img src="https://latex.codecogs.com/gif.latex?v_{\pi }(s)"/>改进策略<img src="https://latex.codecogs.com/gif.latex?\pi "/>
  - 典型的如贪婪策略提升

# 策略迭代
- ## 流程
  - 策略评价(迭代k次，直到接近收敛为止) + 策略提升(提升1次)
- ## 终止条件
  - 提升停止
