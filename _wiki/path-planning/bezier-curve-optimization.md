---
layout: wiki
title: Bezier Curve Optimization
categories: [path-planning]
description: description of Bezier Curve Optimization
keywords: bezier, optimization
---

# 核心
- ## 利用Bernstein polynomial轨迹表达式代替一般的polynomial轨迹表达式。
  - ### 轨迹原始表达式
    - <img src="https://latex.codecogs.com/gif.latex?P_{j}(t)=p_{j}^{0}+p_{j}^{1}t+p_{j}^{2}t^{2}+...+p_{j}^{n}t^{n}"/>
  - ### Bernstein polynomial轨迹表达式
    - <img src="https://latex.codecogs.com/gif.latex?B_{j}(t)=c_{j}^{0}b_{n}^{0}(t)+c_{j}^{1}b_{n}^{1}(t)+...+c_{j}^{n}b_{n}^{n}(t)"/>
    - <img src="https://latex.codecogs.com/gif.latex?b_{n}^{i}(t)=\binom{n}{i}\cdot t^{i}\cdot (1-t)^{n-i}"/>
  - ### 其中，<img src="https://latex.codecogs.com/gif.latex?j"/>表示该段轨迹的序号，<img src="https://latex.codecogs.com/gif.latex?t"/>表示时间。
  - ### 另外，原始表达式中<img src="https://latex.codecogs.com/gif.latex?p_{j}^{i}"/>为多项式的系数，没有实际的物理意义。而Bernstein polynomial轨迹表达式中的<img src="https://latex.codecogs.com/gif.latex?c_{j}^{i}"/>具有实际的物理意义，即控制点的坐标。

---

# 性质
- ## Endpoint interpolation
  > 在给出一系列控制点后，对应的Bezier曲线只保证穿过第一个和最后一个控制点，中间的其他控制点不要求穿过，但会对bezier曲线的形状产生影响。
- ## Convex hull
  > 生成的Bezier曲线一定不会超出由控制点形成的凸多边形的包围框。
- ## Hodograph
  > <img src="https://latex.codecogs.com/gif.latex?B_{j}(t)"/>的导数<img src="https://latex.codecogs.com/gif.latex?B_{j}^{'}(t)"/>依然是一个Bezier曲线，且该曲线的控制点为<img src="https://latex.codecogs.com/gif.latex?n\cdot (c^{_{i+1}}-c^{_{i}})"/>。
- ## Fixed time interval
  > Bezier曲线的时间<img src="https://latex.codecogs.com/gif.latex?t"/>永远定义在[0,1]区间内。

---

# 工作流程
- ## 求解飞行走廊
  - 构建栅格地图（比较好的比如八叉树格式），存储和搜索效率较高。
  - 在栅格地图中利用A*算法求解无碰撞路径。
  - 对路径中经过的每个栅格进行膨胀，四个边向四周扩张，直到遇到障碍物停止，形成安全走廊。若相邻两个安全走廊是一样的，就合并为一个。
  - 得到一串有序的安全走廊。
- ## 基于飞行走廊和Bernstein多项式的轨迹优化
  - 起点状态和终点状态等式约束
  - 相邻两段轨迹的连接点处的状态连续性约束
  - 相邻两段轨迹的连接点需要处于对应的两个安全走廊的重叠区域内
  - 满足动力学（速度、加速度）约束，只要保证<img src="https://latex.codecogs.com/gif.latex?B_{j}(t)"/>的导数表达式<img src="https://latex.codecogs.com/gif.latex?B_{j}^{'}(t)"/>和<img src="https://latex.codecogs.com/gif.latex?B_{j}^{''}(t)"/>的控制点在<img src="https://latex.codecogs.com/gif.latex?Vel_{min}"/>、<img src="https://latex.codecogs.com/gif.latex?Vel_{max}"/>和<img src="https://latex.codecogs.com/gif.latex?Acc_{min}"/>、<img src="https://latex.codecogs.com/gif.latex?Acc_{max}"/>内。

---

# 扩展
- ## 基于B-spline的多项式表达，Bezier是一种特殊的B-spline。B-spline自动分段，不需要人为将完整轨迹分为N段，从而避免段与段之间的连续性分析。