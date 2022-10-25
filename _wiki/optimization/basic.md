---
layout: wiki
title: Basic of Optimization 
categories: [optimization]
description: description of optimization 
keywords: optimization 
---

# 参考书籍
- ### 最优化：建模、算法与理论
  - 中文版
- ### Numerical Optimization
  - 理论很全
  - 关注实数在程序中的表示方法导致的数值稳定性问题，给了很多工程上的实践，帮助读者写出更加鲁棒稳定的算法
- ### Lectures on Convex Optimization
  - 理论清晰，涵盖很全
- ### Lecture on Modern Convex Optimization
  - 对Conic Programming有比较好的分析和应用

# 优化问题
- 一般形式
  ```
  min f(x)
  s.t. g(x) <= 0, h(x) = 0

  ```
- 问题有解的条件
  - f(x) is lower bounded(有下界)
    - 在x的取值范围内，f(x) >= alpha
  - f(x) is bounded level set
    - 满足f(x) < beta的x的取值有上下界，f = 1/x, (x > 0)就不满足，x到无穷大时f最小

- 机器人领域中常见的优化问题
  - SLAM: Nolinear Least Squares
  - Trajectory: Nolinear Program
  - Registration: Semi-Definite Program
  - Time Optimal Path Parameterization: Second-Order Conic Program

# 凸集
- 如果集合内的任意两点的连线仍然在集合内，则集合是一个凸集
- convex hull: 点集中所有点的convex combinations的并集
- 常见的凸集合
  - hyperplane: Ax = b
  - half-space: Ax >= b 
  - sphere: \|\|x - x0\|\| = b
  - ball : \|\|x - x0\|\| <= b
  - polynomials: 凸包
  - cone: 锥(不一定是凸的)
    - 半定锥(一定是凸的)
- 凸集的交集一定是凸的
- 凸集的并集不一定凸
- 凸集的叉乘一定是凸

# 函数的高阶导数
- 一阶导数: gradient
- 二阶导数: hessian
- hessian是gradient的jacobian

# 矩阵求导
- [网址](https://en.wikipedia.org/wiki/Matrix_calculus)
- > dA = 0
- > d(aX) = ad(X)
- > d(AXB) = Ad(X)B
- > d(X + Y) = dX + dY
- > d(X^t) = (dX)^t
- > d(XY) = (dX)Y + X(dY)
- > d\<X, Y\> = \<dX, Y\> + \<X, dY\>
- > d(X/Phi) = (phi(dX) - (dPhi)X) / (Phi)^2
- > d(tr(X)) = I
- > d(f(g(x))) = (df/dg)\*dg(x)

# 凸函数及其性质
- f(ax + (1-a)y) <= af(x) + (1-a)f(y)
  - 如果严格<，称为strictly convex function
- 凸函数一定有convex sub level set
- quasi-convex(拟凸函数)
  - f(x) = log(\|x\| + 1)
  - 拟凸函数的线性加和不一定还是拟凸函数，但凸函数的线性加和肯定是凸函数
- 凸函数的局部最小值一定是全局最小值
- 凸函数的局部最小值的集合一定是凸集
- 凸函数的很多operation是可以保留其凸性的
  - 加权和
  - 范数
  - 仿射变换
  - point-wise max
