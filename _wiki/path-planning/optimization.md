---
layout: wiki
title: Optimization
categories: [path-planning]
description: description of optimization of path planning
keywords: optimization
---

# 优化目标
- ## Minimum Snap Optimization
  - 轨迹的能量消耗最小
  - 7次多项式拟合
- ## Minimum Jerk Optimization
  - 轨迹的舒适度最高
  - 5次多项式拟合

# 约束
- ## 约束项
  - 起点终点状态约束
  - 中间点位置约束
  - 连续性约束
  - 障碍物避碰（带入非凸性)
- ## 约束类型
  - ## 硬约束
    - 一旦优化过程中出现不满足约束条件的状态，问题就不可解
  - ## 软约束
    - 即使出现不满足约束条件的状态，依然可以继续求解最优解

# 时间分配
  - 在轨迹优化的过程中，相邻两个全局离散点中间的运动时间是需要事先设定的。
  - 时间分配将很大程度影响到轨迹的形状
- ## 简单方法
  - 假设两点间的运动都是加速到最大，减速到0的过程，计算消耗时间
  - 假设两点间的运动是期望速度的匀速运动
  - #### 简单方法看起来很傻，但是在基于飞行走廊的轨迹优化中效果很好，因为飞行走廊实际上给了中间点位置调整的空间。
- ## 最优方法
  - 将整条轨迹总时间加入优化目标
  - 将每一段所分配的时间加入待优化目标中
  - 求解优化结果

# 凸优化问题解决方法
- ## 方法
  - 闭式解
  - Linear Programming (LP)
  - Quadratic Programming (QP)
  - Quadratically Constrained QP (QCQP)
  - Second-Order Cone Programming (SOCP)
- ## 库
  - CVX(matlab)
  - Mosek(可以解决几乎所有的凸优化问题，非常鲁棒，只提供x86执行文件)
  - OOQP(可求解二次规划，非常快速，代码开源)
  - GLPK(可求解线性规划，非常快速，代码开源)
  - OSQP(百度Apollo用的二次规划求解器)

# 数值稳定性
- ## Normalization
  - ### Time normalization
    - 在对全局路径点进行分段优化的时候，每一段起点的时间Tn都设为0，终点时间Tn+1 = Tn+1 - Tn
  - ### Problem scale (spatial) normalization
    - 假如轨迹优化是在一个大尺度场景下进行，路点的坐标在数值会有巨大的差异，需要通过某些手段将坐标值进行预处理

# 一些工程问题
- ## 对三个轴同时优化好还是分别优化好？
  - 分别优化更加稳定和快速
- ## 闭式解永远是更好的吗？
  - 当矩阵运算非常消耗资源时，数值优化更加鲁棒些
  - Mosek非常非常的稳定
- ## 多项式可以在任何场合下作为最优轨迹的表达式吗？
  - 大部分时候可以，但也有例外
  - 当优化目标不是单一的平方项的时候(jerk\*jerk + snap\*snap)，多项式形式就不是轨迹的最优解形式了
