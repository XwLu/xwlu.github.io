---
layout: wiki
title: Dynamic Planning
categories: [reinforcement-learning]
description: wiki on Reinforcement Learning
keywords: reinforcement-learning, dynamic-planning
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
  - {策略评价(迭代k次，直到接近收敛为止) + 策略提升(提升1次)}; ...
  - v1 → π1 → v2 → π2 → v3 → π3 → ...
- ## 终止条件
  - 提升停止
- ## 特点
  - 有显式的策略
  - 迭代过程中的值函数对应了某个具体的策略
  - 效率较低
  - 贝尔曼期望方程 + 贪婪策略提升

# 值迭代
- ## 流程
  - 策略评价(迭代1次); ...
  - v1 → v2 → v3 → ...
- ## 特点
  - 没有显式的策略
  - 迭代过程中的值函数可能不对应任何策略
  - 效率较高
  - 贝尔曼期望方程

# 扩展
- ## 异步动态规划
  - 以某种顺序单独考虑每一个状态
  - 能够大大减少计算量
  - 只要所有的状态都能被持续的选择到,收敛性能够保证
  - ### 常用的三种形式
    - 就地(In-Place)动态规划
    - 优先清理
    - 实时动态规划

# 就地(In-Place)动态规划
- 同步值迭代存储了两个版本的值函数，在计算<img src="https://latex.codecogs.com/gif.latex?v_{new}"/>的时候，使用了<img src="https://latex.codecogs.com/gif.latex?v_{old}"/>的复制版本，在整个更新过程中，<img src="https://latex.codecogs.com/gif.latex?v_{old}"/>是不变的，保持上一个循环的状态。
- 就地(In-Place)动态规划只对一个值函数<img src="https://latex.codecogs.com/gif.latex?v_{new}"/>进行更新，因此，从左上角开始更新和从右下角开始更新，得到的结果是不一样的。
