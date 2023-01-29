---
layout: wiki
title: Lagrangian Relaxation
categories: [optimization]
description: use lagrangian relaxation to solve constrained optimization problem
keywords: optimization 
---
# 问题形式
- 原问题：<img src="https://latex.codecogs.com/svg.image?min_{x}f(x)"/>，<img src="https://latex.codecogs.com/svg.image?s.t.Ax=b"/>，其中<img src="https://latex.codecogs.com/svg.image?f(x)"/>是个凸函数
- 原问题的Lagrangian：<img src="https://latex.codecogs.com/svg.image?\L(x,\lambda):=f(x)+\left<\lambda,Ax-b\right>"/>，后面的尖括号是内积的操作符
- 显然：<img src="https://latex.codecogs.com/svg.image?max_{\lambda}f(x)+\left<\lambda,Ax-b\right>=\left\{\begin{matrix}f(x),&Ax-b\\\infty,&otherwise\\\end{matrix}\right."/>
- 因此，原问题等于：<img src="https://latex.codecogs.com/svg.image?min_{x}f(x),s.t.Ax=b\Leftrightarrow&space;min_{x}max_{\lambda}\L(x,\lambda)"/>
- 几何解释
  - ![lagrangian geometry](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/constrained_optimization/lagrangian_geometry.png?raw=true)
  - 最优解即Lagrangian函数的鞍点
  - 从上图可以看到，<img src="https://latex.codecogs.com/svg.image?x>1"/>时，<img src="https://latex.codecogs.com/svg.image?\lambda"/>取无穷大，即可让Lagrangian函数趋于无穷大；<img src="https://latex.codecogs.com/svg.image?x<1"/>时，<img src="https://latex.codecogs.com/svg.image?\lambda"/>取负无穷大，即可让Lagrangian函数趋于无穷大；只有当<img src="https://latex.codecogs.com/svg.image?x=1"/>时，<img src="https://latex.codecogs.com/svg.image?\lambda"/>无论取什么值，Lagrangian函数的值都保持在一个上界之下

# Uzawa's Method
- 推导过程
  - <img src="https://latex.codecogs.com/svg.image?min_{x}max_{\lambda}\L(x,\lambda):=f(x)+\left<\lambda,Ax-b\right>"/>的后半部分<img src="https://latex.codecogs.com/svg.image?max_{\lambda}\L(x,\lambda):=f(x)+\left<\lambda,Ax-b\right>"/>的取值是<img src="https://latex.codecogs.com/svg.image?f(x)"/>或<img src="https://latex.codecogs.com/svg.image?\infty"/>，即对于前面的<img src="https://latex.codecogs.com/svg.image?x"/>来说，Lagrangian函数是不连续的，导致这个min问题很难求
  - 现在假设我们把min和max交换一下顺序，<img src="https://latex.codecogs.com/svg.image?max_{\lambda}min_{x}\L(x,\lambda):=f(x)+\left<\lambda,Ax-b\right>"/>，并且，如果后半部分<img src="https://latex.codecogs.com/svg.image?min_{x}\L(x,\lambda):=f(x)+\left<\lambda,Ax-b\right>"/>是严格凸的话，给定一个<img src="https://latex.codecogs.com/svg.image?\lambda"/>就可以很轻易的求解<img src="https://latex.codecogs.com/svg.image?x^{*}"/>
  - 注意！von Neumann提到：<img src="https://latex.codecogs.com/svg.image?max_{\lambda}min_{x}\L(x,\lambda)\leq&space;min_{x}max_{\lambda}\L(x,\lambda)"/>，当且仅当<img src="https://latex.codecogs.com/svg.image?f(x)"/>连续且凸时取等号
  - 因此我们直接通过梯度上升法求解对<img src="https://latex.codecogs.com/svg.image?\lambda"/>做maximize的对偶问题：<img src="https://latex.codecogs.com/svg.image?d(\lambda):=min_{x}f(x)+\left<\lambda,Ax-b\right>"/>
    - 由于每确定一个<img src="https://latex.codecogs.com/svg.image?\lambda"/>，都可以求得一个确定的<img src="https://latex.codecogs.com/svg.image?x^{*}"/>，所以<img src="https://latex.codecogs.com/svg.image?x^{*}"/>的值本质上是关于<img src="https://latex.codecogs.com/svg.image?\lambda"/>的函数，因此上面的对偶问题又可以写成<img src="https://latex.codecogs.com/svg.image?d(\lambda)=\L(x^{*}(\lambda),\lambda)"/>
    - 我们将它对<img src="https://latex.codecogs.com/svg.image?\lambda"/>求导，<img src="https://latex.codecogs.com/svg.image?\frac{\partial&space;d}{\partial\lambda}=\frac{\partial\L}{\partial&space;x^{*}}\cdot\frac{\partial&space;x^{*}}{\partial\lambda}+\frac{\partial\L}{\partial\lambda}"/>，注意<img src="https://latex.codecogs.com/svg.image?\frac{\partial\L}{\partial&space;x^{*}}"/>就是0，对于凸函数来说，导数为0的点就是最优解所在的点，所以<img src="https://latex.codecogs.com/svg.image?\frac{\partial&space;d}{\partial\lambda}=\frac{\partial\L}{\partial\lambda}|_{x^{*}(\lambda)}"/>，也就说，我们给定一个<img src="https://latex.codecogs.com/svg.image?\lambda"/>，可以确定一个最优的<img src="https://latex.codecogs.com/svg.image?x^{*}"/>，然后用<img src="https://latex.codecogs.com/svg.image?x^{*}"/>求得<img src="https://latex.codecogs.com/svg.image?\frac{\partial&space;d}{\partial\lambda}=Ax^{*}-b"/>后，通过梯度上升求得新的<img src="https://latex.codecogs.com/svg.image?\lambda"/>.
    - 总结一下，迭代步骤如下：
      - <img src="https://latex.codecogs.com/svg.image?\left\{\begin{matrix}x^{k+1}=argmin_{x}\L(x,\lambda^{k})\\\lambda^{k+1}=\lambda^{k}+\alpha(Ax^{k+1}-b)\end{matrix}\right."/>
      - <img src="https://latex.codecogs.com/svg.image?\alpha"/>是沿梯度方向前进的步长
- 缺陷
  - (致命)该方法要求<img src="https://latex.codecogs.com/svg.image?f(x)"/>是凸的
  - (致命)该方法要求<img src="https://latex.codecogs.com/svg.image?min_{x}\L(x,\lambda):=f(x)+\left<\lambda,Ax-b\right>"/>关于原问题的优化变量<img src="https://latex.codecogs.com/svg.image?x"/>是严格凸的
  - 梯度上升的步长需要调参
  - 收敛速度不一定快