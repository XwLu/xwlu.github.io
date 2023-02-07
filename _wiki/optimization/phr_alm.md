---
layout: wiki
title: PHR Augmented Lagrangian Method
categories: [optimization]
description: Powell-Hestenes-Rockafellar Method for Equality- Constrained Cases
keywords: optimization 
---
# 背景
- PHR是Powell、Hestenes和Rockafellar三个人名的缩写，前两个人在等式约束的优化问题中提出了增广拉格朗日乘子法，第三个作者将其推广到不等式约束上，并给出了新的解释

# 等式约束
- 问题形式
  - <img src="https://latex.codecogs.com/svg.image?min_{x\in\mathbb{R^{n}}}f(x)"/>
  - <img src="https://latex.codecogs.com/svg.image?s.t.h(x)=0"/>
- 推导
  - Uzawa's method
    - 通过冯诺依曼定理将minmax问题转化为maxmin（对偶）问题，只需要满足原问题是严格凸的，原问题和对偶问题的最优解就是一致的
    - <img src="https://latex.codecogs.com/svg.image?d(\lambda):=min_{x}f(x)+\lambda^{T}h(x)"/>
    - 如果原问题不是严格凸，上面的式子求得的<img src="https://latex.codecogs.com/svg.image?x^{*}"/>就不是唯一的，导致<img src="https://latex.codecogs.com/svg.image?d(\lambda)"/>不是光滑的，梯度<img src="https://latex.codecogs.com/svg.image?\triangledown&space;d(\lambda)"/>有可能不存在
  - PHR Augmented Lagrangian Method
    - 核心思路
      - 不直接依赖冯诺依曼定理将minmax问题转化为maxmin问题。
      - 再来观察一下原问题
        - <img src="https://latex.codecogs.com/svg.image?min_{x}max_{\lambda}f(x)+\lambda^{T}h(x)"/>
      - 现在的主要难点在于，函数的max部分，取值要么是无穷大要么是一个定值，他是一个non smooth的函数，一个很直观的想法是，把这部分近似一下，变成一个smooth的函数：
        - 我们在右边加一个proximal point项。其思路是，我们并不知道<img src="https://latex.codecogs.com/svg.image?\lambda"/>最优应该取什么值，但可以先假设<img src="https://latex.codecogs.com/svg.image?\lambda"/>的最优解是<img src="https://latex.codecogs.com/svg.image?\bar\lambda"/>，那么我们在优化的时候尽可能让<img src="https://latex.codecogs.com/svg.image?\lambda"/>靠近<img src="https://latex.codecogs.com/svg.image?\bar\lambda"/>
        - <img src="https://latex.codecogs.com/svg.image?min_{x}max_{\lambda}f(x)+\lambda^{T}h(x)-\frac{1}{2\rho}\left\|\lambda-\bar{\lambda}\right\|"/>，<img src="https://latex.codecogs.com/svg.image?(\rho>0)"/>
        - 加上这个proximal point项（正则项）之后，原问题的max部分就是对<img src="https://latex.codecogs.com/svg.image?\lambda"/>的线性项加上对<img src="https://latex.codecogs.com/svg.image?\lambda"/>的二次项，这对<img src="https://latex.codecogs.com/svg.image?\lambda"/>来说是一个连续且严格凸的函数，且二次函数的最优值是有解析解的
          - ![proximal point term](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/constrained_optimization/proximal_point_term.png?raw=true)
          - ![phr outer](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/constrained_optimization/phr_outer.png?raw=true)
          - 至此，该问题变成了一个针对<img src="https://latex.codecogs.com/svg.image?x"/>无约束优化问题
      - 但目前我们只是得到了对原问题的minmax解的一个粗略估计，仍需要持续迭代提高精度，从两个角度进行提升
        - 让正则项趋近于0，即<img src="https://latex.codecogs.com/svg.image?\rho\to\infty"/>
        - 不断更新更准确的<img src="https://latex.codecogs.com/svg.image?\bar\lambda"/>，<img src="https://latex.codecogs.com/svg.image?\bar\lambda\leftarrow\lambda^{*}(\bar\lambda)"/>。因为第一次优化时，<img src="https://latex.codecogs.com/svg.image?\bar\lambda"/>是我们猜的，优化结束后，我们得到的<img src="https://latex.codecogs.com/svg.image?\lambda^{*}"/>比之前猜的<img src="https://latex.codecogs.com/svg.image?\bar\lambda"/>更接近最优解，所以第二次就把<img src="https://latex.codecogs.com/svg.image?\lambda^{*}"/>作为下一轮的<img src="https://latex.codecogs.com/svg.image?\bar\lambda"/>来不断提高精度
        - 注意，由于我们不断地在更新更精确的<img src="https://latex.codecogs.com/svg.image?\bar\lambda"/>，这意味着<img src="https://latex.codecogs.com/svg.image?\left\|\lambda-\bar{\lambda}\right\|"/>本身不断在接近0，所以<img src="https://latex.codecogs.com/svg.image?\rho"/>的取值不需要真的趋向无穷大， 慢慢增长到一定程度大即可,取到1000就可以了
      - 现在我们不需要借助冯诺依曼定理最minmax问题进行对换了，也就不需要保证<img src="https://latex.codecogs.com/svg.image?f(x)"/>严格凸
    - 原问题的拉格朗日函数 + 增广项 = 增广拉格朗日法
      - ![phr alm func](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/constrained_optimization/phr_alm_func.png?raw=true)
    - 一般来说，我们常把上面的式子整理为如下形式（灰色部分可以省略），进行迭代
      - ![frequently phr alm](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/constrained_optimization/frequently_phr_alm.png?raw=true)

# 不等式约束
- 问题形式
  - <img src="https://latex.codecogs.com/svg.image?min_{x\in&space;R^{n}}f(x)"/>
  - <img src="https://latex.codecogs.com/svg.image?s.t.g(x)\leq0"/>
- 原问题变形（不等式变等式）
  - <img src="https://latex.codecogs.com/svg.image?min_{x\in&space;R^{n},s\in&space;R^{m}}f(x)"/>
  - <img src="https://latex.codecogs.com/svg.image?s.t.g(x)+\left[s\right]^{2}\leq0"/>，其中<img src="https://latex.codecogs.com/svg.image?\left[\cdot\right]^{2}"/>表示element-wise squaring
  - ![phr alm inequal](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/constrained_optimization/phr_alm_inequal.png?raw=true)

# 等式约束+不等式约束
- 问题形式
  - <img src="https://latex.codecogs.com/svg.image?min_{x\in&space;R^{n}}f(x)"/>
  - <img src="https://latex.codecogs.com/svg.image?s.t.g(x)\leq0,h(x)=0"/>
- PHR_Augmented Lagrangian
  - <img src="https://latex.codecogs.com/svg.image?\pounds_{\rho}(x,\lambda,\mu):=f(x)+\frac{\rho}{2}\left\{\left\|h(x)+\frac{\lambda}{\rho}\right\|^{2}+\left\|max\left[g(x)+\frac{\mu}{\rho},0\right%20])\right\|\right\}-\frac{1}{2\rho}\left\{\left\|\lambda\right\|^{2}%20+%20\left\|\mu\right\|^{2}\right\}"/>
    - 丄式的最后一项一般都省略掉
    - <img src="https://latex.codecogs.com/svg.image?\rho>0,\mu\geq0"/>
- 迭代步骤
  - <img src="https://latex.codecogs.com/svg.image?\left\{\begin{matrix}x\leftarrow%20argmin_{x}\pounds_{\rho}(x,\lambda,\mu)\\\lambda\leftarrow\lambda+\rho%20h(x)\\\mu\leftarrow%20max\left[\mu+\rho%20g(x),0\right%20]\\\rho\leftarrow%20min\left[(1+\lambda)\rho,\beta\right]\end{matrix}\right."/>
- 参数初始化
  - <img src="https://latex.codecogs.com/svg.image?\rho=1,\lambda=\mu=0,\gamma=1,\beta=10^{3}"/>
- 内层循环退出条件（求<img src="https://latex.codecogs.com/svg.image?argmin_{x}"/>）
  - <img src="https://latex.codecogs.com/svg.image?\left\|\triangledown_{x}\pounds_{\rho}(x,\lambda,\mu)\right\|_{\infty}%3C\xi^{k}min\left[1,max\left[\left\|h(x)\right\|_{\infty},\left\|max\left[g(x),-\frac{\mu}{\rho}\right]\right\|_{\infty}\right]\right]"/>
  - <img src="https://latex.codecogs.com/svg.image?\xi"/>从一个正数逐渐收敛到0
- 外层迭代退出条件
  - <img src="https://latex.codecogs.com/svg.image?max\left[\left\|h(x)\right\|_{\infty},\left\|max\left[g(x),-\frac{\mu}{\rho}\right]\right\|_{\infty}\right]%3C\epsilon_{cons}"/>
  - <img src="https://latex.codecogs.com/svg.image?\left\|\triangledown_{x}\pounds_{\rho}(x,\lambda,\mu)\right\|_{\infty}%3C\epsilon_{prec}"/>