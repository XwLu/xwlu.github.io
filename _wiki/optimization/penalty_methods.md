---
layout: wiki
title: Penalty Methods
categories: [optimization]
description: use penalty methods to solve constrained optimization problem
keywords: optimization 
---
# L2 Penalty Method
## 问题形式
- ### 等式约束
  - 原问题：<img src="https://latex.codecogs.com/svg.image?min_{x}f(x)"/>，<img src="https://latex.codecogs.com/svg.image?s.t.&space;c_{i}(x)=0,i\in&space;\varepsilon"/>
  - 罚函数形式：<img src="https://latex.codecogs.com/svg.image?P_{E}(x,\sigma)=f(x)&plus;\frac{1}{2}\sigma\sum_{i\in&space;\varepsilon}c_{i}^{2}(x)"/>
  - 上面使用的是二阶罚函数法，求解精度取决于<img src="https://latex.codecogs.com/svg.image?\sigma"/>的大小，但无法求得精确解
- ### 不等式约束
  - 原问题：<img src="https://latex.codecogs.com/svg.image?min_{x}f(x)"/>，<img src="https://latex.codecogs.com/svg.image?s.t.c_{i}(x)\leq&space;0,i\in&space;I"/>
  - 罚函数形式：<img src="https://latex.codecogs.com/svg.image?P_{I}(x,\sigma)=f(x)&plus;\frac{1}{2}\sigma\sum_{i\in&space;I}max\left[c_{i}(x),0\right]^{2}"/>
  - 上面使用的也是二阶罚函数法，但罚函数的二阶导不连续，无法求得精确解

## 迭代过程
- 直接法
  - 直接取一个很大的<img src="https://latex.codecogs.com/svg.image?\sigma"/>，优化一次得到最优解
- sequential方法
  - 取<img src="https://latex.codecogs.com/svg.image?\sigma=1"/>，优化得到最优解<img src="https://latex.codecogs.com/svg.image?x^{1}"/>
  - 取<img src="https://latex.codecogs.com/svg.image?\sigma=10"/>，以<img src="https://latex.codecogs.com/svg.image?x^{1}"/>为初值，优化得到最优解<img src="https://latex.codecogs.com/svg.image?x^{2}"/>
  - 重复该过程，直到<img src="https://latex.codecogs.com/svg.image?\sigma"/>足够大
- 具体用哪种方法取决于对耗时和精度的要求


## 使用场景
- 约束最好具有具体的物理意义，因为该方法是得不到精确解的，且最终解实际上会一定程度上违反约束
- 对精度要求不是很高，在<img src="https://latex.codecogs.com/svg.image?10^{-3}"/>量级左右

# L1 Penalty Method
## 问题形式
- 原问题：<img src="https://latex.codecogs.com/svg.image?min_{x}f(x)"/>，<img src="https://latex.codecogs.com/svg.image?s.t.c_{i}(x)=0,i\in\varepsilon"/>，<img src="https://latex.codecogs.com/svg.image?c_{j}(x)\leq&space;0,j\in&space;I"/>
- 罚函数形式：<img src="https://latex.codecogs.com/svg.image?P(x,\sigma)=f(x)&plus;\sigma\sum_{i\in&space;\varepsilon}\left|c_{i(x)}\right|&plus;\sigma\sum_{j\in&space;I}max\left[c_{j}(x),0\right]"/>
- 上面使用的是一阶罚函数法，罚函数的一阶导不连续，当<img src="https://latex.codecogs.com/svg.image?\sigma"/>充分大时（不用像L2方法一样取那么大），可以得到**精确解**
- 虽然可以得到精确解，但由于其non smooth的性质，lbfgs在求解该类型问题时收敛速度是不能保证的