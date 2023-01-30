---
layout: wiki
title: Karush-Kuhn-Tucker(KKT) Conditions
categories: [optimization]
description: kkt conditions for phr-alm
keywords: optimization 
---
# 定理
- 对于问题
  - <img src="https://latex.codecogs.com/svg.image?min_{x}f(x)"/>
  - <img src="https://latex.codecogs.com/svg.image?s.t.%20h_{i}(x)\leq0,i=1,...,m;l_{i}(x)=0,j=1,...,r"/>
- 如果该问题不是degenerate，那么最优解满足如下条件
  - stationarity: <img src="https://latex.codecogs.com/svg.image?0\in\partial_{x}\left[f(x)+\sum_{i=1}^{m}u_{i}h_{i}(x)+\sum_{j=1}^{r}v_{i}l_{i}(x)\right]"/>
  - complementary slackness: <img src="https://latex.codecogs.com/svg.image?u_{i}\cdot%20h_{i}=0,i=1,...,m"/>
  - primal feasibility: <img src="https://latex.codecogs.com/svg.image?h_{i}(x)\leq0,l_{j}=0;i=1,...,m;j=1,...,r"/>
  - dual feasibility: <img src="https://latex.codecogs.com/svg.image?u_{i}\geq0,i=1,...,m"/>

# 用途
- 通过构造KKT条件，如果条件中没有不等式，那就有机会直接求解优化问题的解，比如下图这种情况，我们只需要求解下面的线性方程组的解即可得到优化结果
  - ![example](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/constrained_optimization/kkt_example.png?raw=true)
- 可以拿来度量约束优化数值算法的解的精度。下面三个值是KKT残差，通过取其中的最大值，使其足够小，就可以得到精度较高的解
  - <img src="https://latex.codecogs.com/svg.image?max\left\{h_{i},0\right\}"/>
    - 不等式约束违背程度
  - <img src="https://latex.codecogs.com/svg.image?\left|l_{j}\right|"/>
    - 等式约束违背程度
  - <img src="https://latex.codecogs.com/svg.image?\left\|\partial_{x}\left[f(x)+\sum_{i=1}^{m}u_{i}h_{i}(x)+\sum_{j=1}^{r}v_{i}l_{i}(x)\right]\right\|"/>
    - 梯度残差