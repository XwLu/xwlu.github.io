---
layout: wiki
title: Unconstrained Optimization 
categories: [optimization]
description: unconstrained optimization 
keywords: optimization 
---

# Steepest Gradient Descent
### 最速下降法
- 沿着梯度（grad或least-norm sub-grad）的负方向更新
  - <img src="https://latex.codecogs.com/svg.image?x^{k&plus;1}=x^{k}-\tau&space;\triangledown&space;f(x^{k})"/>
- 步长选择
  - Constant step size（固定步长）
    - <img src="https://latex.codecogs.com/svg.image?\tau=c"/>
  - Diminishing step size（渐消步长）
    - <img src="https://latex.codecogs.com/svg.image?\tau=c/k"/>
    - Robbins-Monro rule for expensive function
    - 鲁棒性很强，一定可以收敛到local minimize
    - 如果函数是非光滑的或者梯度的计算存在一个方差较大的噪声时，可以用这种方法，但收敛速度相对比较慢
  - Exact line search（精确线搜索）
    - <img src="https://latex.codecogs.com/svg.image?\tau=argmin_{\alpha}f(x^{k}&plus;\alpha&space;d)"/>
    - 找到一个<img src="https://latex.codecogs.com/svg.image?\alpha"/>使得函数下降最多，这本身就是个优化问题（将多维优化变成了一维优化），求解很难，因此工程中常用下面一种方法
  - Inexact line search（非精确线搜索）
    - <img src="https://latex.codecogs.com/svg.image?\tau\in\left\{&space;\alpha&space;|&space;f(x^{k})&space;-&space;f(x^{k}&plus;\alpha&space;d)\geq&space;-c\cdot&space;\alpha&space;d^{T}\triangledown&space;f(x^{k})\right\}"/>
    - Armijo condition（充分下降条件）
      - ![Armijo line search](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/unconstrained_optimization/armijo_line_search.png?raw=true)
      - <img src="https://latex.codecogs.com/svg.image?\tau"/>初始化为1.0，<img src="https://latex.codecogs.com/svg.image?c\in&space;(0,1)"/>
      - <img src="https://latex.codecogs.com/svg.image?\tau"/>每个循环都减半，直到函数值第一次落到上图虚线的下方，结束循环，更新<img src="https://latex.codecogs.com/svg.image?x"/>
      - 注意，函数只有次梯度的话需要沿着least norm grad的反方向更新，循环结束条件是次梯度包含0向量
    - Inexact line search虽然迭代次数比Exact line search更多，但每次迭代的耗时少很多，因此大部分情况下总时间也少
- 缺点
  - ![sgd drawback](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/unconstrained_optimization/sgd_drawback.png?raw=true)
  - 从上图可以看出，条件数为2的时候，sgd就需要震荡多次才能到最优解，当条件数非常大的时候（椭圆的上下沿接近平行），那更新过程就会震荡没完了。因此为了更快收敛，我们除了利用梯度信息，还需要用到曲率信息

# Modified Damped Newton's Method
### 修正阻尼牛顿法
