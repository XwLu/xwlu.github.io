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

# Newton's Method
### 牛顿法
- 前提条件
  - 一阶导和二阶导连续
  - 具有严格正定的Hessian矩阵
- 原理
  - 对函数进行二阶泰勒展开
    - <img src="https://latex.codecogs.com/svg.image?f(x)\approx&space;f(x_{k})&plus;\triangledown&space;f(x_{k})^{T}(x-x_{k})&plus;\frac{1}{2}(x-x_{k})^{T}\triangledown&space;^{2}f(x_{k})(x-x_{k})"/>
  - 导数为0时得到极值点
    - <img src="https://latex.codecogs.com/svg.image?\triangledown&space;f(x)=\triangledown&space;^{2}&space;f(x_{k})(x-x_{k})&plus;\triangledown&space;f(x_{k})=0"/>
    - <img src="https://latex.codecogs.com/svg.image?x=x_{k}-\left&space;[&space;\triangledown&space;^{2}f(x_{k})&space;\right&space;]^{-1}\triangledown&space;f(x_{k})"/>
  - 更新公式
    - <img src="https://latex.codecogs.com/svg.image?x_{k&plus;1}=x_{k}-\left&space;[&space;\triangledown&space;^{2}f(x_{k})&space;\right&space;]^{-1}\triangledown&space;f(x_{k})"/>
    - 减号后面的内容就是牛顿步长（Newton step），这里需要满足Hessian严格正定<img src="https://latex.codecogs.com/svg.image?\triangledown^{2}&space;f(x_{k})>0"/>，因为沿着梯度的负方向才可以保证函数值下降，牛顿步长是一个负号乘Hessian的逆再乘梯度，Hessian的逆必须要和梯度是同向的（正定）才行，从而Hessian也必须是正定的
  - 特别的，如果目标函数本身就是二次的，那一步就可以迭代到最优解
- 与梯度下降法对比
  - ![newton v.s. gd](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/unconstrained_optimization/newton_vs_gd.png?raw=true)
  - 下降路线更加平直
  - 迭代次数更少
  - 每次迭代计算量更大
- 缺点
  - 很多时候Hessian矩阵是半正定或者不定的，会导更新方向不是梯度的反方向，函数值增大
  - ![Hessian indefinite](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/unconstrained_optimization/hessian_indef.png?raw=true)

# Modified Damped Newton's Method
### 修正阻尼牛顿法
- 背景
  - 提高牛顿方法在一般函数上的鲁棒性
  - 对牛顿法的优化思路
    - ![Modified Damped Newton's Method](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/unconstrained_optimization/modified_damped_newton.png?raw=true)
    - 考虑到Hessian不一定正定，尝试用一个正定的<img src="https://latex.codecogs.com/svg.image?M"/>拟合Hessian，只要拟合的够接近，也可以近似表示曲率信息
    - 其实没必要求<img src="https://latex.codecogs.com/svg.image?M^{-1}"/>，本质上我们是想求线性方程<img src="https://latex.codecogs.com/svg.image?Md=-\triangledown&space;f(x)"/>的解<img src="https://latex.codecogs.com/svg.image?d"/>，可以用一些成熟的线性求解器进行求解，比解逆快很多
    - inexact line search不需要求Hessian，梯度也只是在最开始算一次，更新<img src="https://latex.codecogs.com/svg.image?t"/>的过程中不需要计算梯度，所以计算量很小
- 原问题
  - <img src="https://latex.codecogs.com/svg.image?\left&space;[&space;\triangledown&space;^{2}f(x)&space;\right&space;]d=-\triangledown&space;f(x)"/>
- 用<img src="https://latex.codecogs.com/svg.image?M"/>拟合Hessian
  - 如果是凸函数（Hessian半正定）
    - <img src="https://latex.codecogs.com/svg.image?M=\triangledown&space;^{2}f(x)&plus;\epsilon&space;I,\epsilon&space;=min(1,\left\|&space;\triangledown&space;f(x)&space;\right\|_{\infty&space;})/10"/>
    - <img src="https://latex.codecogs.com/svg.image?M"/>严格正定，搜索方向的求解可以用Cholesky factorization快速求解
    - <img src="https://latex.codecogs.com/svg.image?Md=-\triangledown&space;f(x),M=LL^{T}"/>，<img src="https://latex.codecogs.com/svg.image?L"/>是个下三角矩阵
  - 如果是非凸函数（Hessian不定）
    - 将Hessian进行Bunch-Kaufman Factorization，原问题转化为<img src="https://latex.codecogs.com/svg.image?LBL^{T}d=-\triangledown&space;f(x)"/>
    - 其中，<img src="https://latex.codecogs.com/svg.image?L"/>是个下三角矩阵，<img src="https://latex.codecogs.com/svg.image?B"/>是个对角线由<img src="https://latex.codecogs.com/svg.image?1\times&space;1"/>和<img src="https://latex.codecogs.com/svg.image?2\times&space;2"/>的矩阵块组成的块对角阵
    - <img src="https://latex.codecogs.com/svg.image?1\times&space;1"/>的标量一定是正数，<img src="https://latex.codecogs.com/svg.image?2\times&space;2"/>的矩阵块的特征值是一正一负，我们需要把每个<img src="https://latex.codecogs.com/svg.image?2\times&space;2"/>矩阵替换为和其最接近的<img src="https://latex.codecogs.com/svg.image?2\times&space;2"/>正定矩阵，最终得到新的<img src="https://latex.codecogs.com/svg.image?\tilde{B}"/>（正定），把<img src="https://latex.codecogs.com/svg.image?d"/>求解出来
      - 上面<img src="https://latex.codecogs.com/svg.image?2\times&space;2"/>矩阵的正定化就是把负的特征值都算出来，然后用一个<img src="https://latex.codecogs.com/svg.image?\epsilon&space;"/>去代替负特征值得到新的矩阵

# Quasi Newton's Method
### 拟牛顿法
- 牛顿法的问题
  - 牛顿法需要函数在任何点的Hessian可逆且正定，条件比较苛刻
  - 牛顿法的计算量太大
  - 当<img src="https://latex.codecogs.com/svg.image?x"/>距离函数的最优解还比较远的时候，用二次函数进行近似的效果不好，这时候用牛顿法不仅计算量大，收敛还很慢；当<img src="https://latex.codecogs.com/svg.image?x"/>距离函数的最优解比较近的时候，二次函数的近似会好一些，收敛会很快。
  - Hessian拟合的函数的条件数可能会变得很大（poorly conditioned）。比如函数是一段直线和一段二次曲线拼接起来的，在直线部分计算Hessian去确定更新步长的话，会得到一个非常大的更新步长（曲率是0，对0取逆是无穷大）
- 拟牛顿法需要满足的一些特质
  - 原理和修正阻尼牛顿法一样，设计一个<img src="https://latex.codecogs.com/svg.image?M"/>去近似<img src="https://latex.codecogs.com/svg.image?H"/>
  - 收敛速度应该在牛顿法和最速梯度下降法之间
  - 不需要计算完整的Hessian矩阵（低计算量）
  - 线性方程<img src="https://latex.codecogs.com/svg.image?Md=-\triangledown&space;f(x)"/>存在闭式解
  - <img src="https://latex.codecogs.com/svg.image?M"/>不应该是一个稠密的阵，只需要在重要的的方向上对<img src="https://latex.codecogs.com/svg.image?H"/>做近似，尽可能稀疏
  - <img src="https://latex.codecogs.com/svg.image?d"/>一定得让函数下降（和梯度方向的夹角小于90度），其实就是<img src="https://latex.codecogs.com/svg.image?M"/>必须正定
  - <img src="https://latex.codecogs.com/svg.image?d"/>应该包含曲率信息（收敛要比梯度下降来的快），也就是满足<img src="https://latex.codecogs.com/svg.image?\Delta&space;g\approx&space;M^{k&plus;1}\Delta&space;x"/>
- 拟牛顿法的核心思路
  - 通过采样N对<img src="https://latex.codecogs.com/svg.image?\Delta&space;x"/>和<img src="https://latex.codecogs.com/svg.image?\Delta&space;g"/>来估计<img src="https://latex.codecogs.com/svg.image?\Delta&space;M"/>
  - 同时，考虑到最终是要求<img src="https://latex.codecogs.com/svg.image?M^{-1}"/>，干脆直接估计<img src="https://latex.codecogs.com/svg.image?B=M^{-1}"/>，更新方向<img src="https://latex.codecogs.com/svg.image?\Delta&space;x=B\Delta&space;g&space;"/>
- 凸且光滑函数的BFGS方法
  - 假设我们有了很多的<img src="https://latex.codecogs.com/svg.image?\Delta&space;x"/>和<img src="https://latex.codecogs.com/svg.image?\Delta&space;g"/>，怎么估计B呢？还是用优化迭代的思路：
    - 初始化<img src="https://latex.codecogs.com/svg.image?B^{0}"/>为单位阵
    - 迭代求解最优的<img src="https://latex.codecogs.com/svg.image?B"/>，迭代的思路如下：
      - 我们希望迭代前后B的差距尽可能小：<img src="https://latex.codecogs.com/svg.image?min_{B^{k+1}}\left\|&space;B^{k+1}-B^{k}&space;\right\|^{2}"/>
      - 其次，<img src="https://latex.codecogs.com/svg.image?B"/>需要满足一些约束：
        - <img src="https://latex.codecogs.com/svg.image?B=B^{T}"/>，这是因为Hessian是对称阵，所以Hessian的逆也应该对称
        - <img src="https://latex.codecogs.com/svg.image?\Delta&space;x=B\Delta&space;g&space;"/>
      - 注意，单纯用差的二范数描述<img src="https://latex.codecogs.com/svg.image?B^{k}"/>和<img src="https://latex.codecogs.com/svg.image?B^{k+1}"/>的变化并不好，比如<img src="https://latex.codecogs.com/svg.image?\begin{bmatrix}&space;100&space;&&space;1&space;\\&space;1&space;&&space;100&space;\\\end{bmatrix}"/>和<img src="https://latex.codecogs.com/svg.image?\begin{bmatrix}&space;100&space;&&space;0.5&space;\\&space;0.5&space;&&space;100&space;\\\end{bmatrix}"/>的差值的二范数很小，但对于右上和左下角的元素来说变化和其自身的大小相比是巨大的，因此需要进行归一化
      - 归一化后，优化目标变成：<img src="https://latex.codecogs.com/svg.image?min_{B^{k&plus;1}}\left\|&space;H^{\frac{1}{2}}(B^{k&plus;1}-B^{k})H^{\frac{1}{2}}&space;\right\|^{2}"/>，<img src="https://latex.codecogs.com/svg.image?B=H"/>为真实的Hessian矩阵，<img src="https://latex.codecogs.com/svg.image?H=\int_{0}^{1}\triangledown^{2}f\left[(1-\tau)x^{k}&plus;\tau&space;x^{k&plus;1}\right]d\tau"/>
      - 我们本来就是套估计H，现在这里还要用到H，看起来是个鸡生蛋，蛋生鸡的问题，但实际上这个问题是有解析解的，与<img src="https://latex.codecogs.com/svg.image?H"/>无关。四个优化领域的大佬提出了BFGS方法，最终得到的更新公式如下：
      - ![BFGS](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/unconstrained_optimization/bfgs.png?raw=true)
      - 注意：当<img src="https://latex.codecogs.com/svg.image?\Delta&space;g^{T}\Delta&space;x>0"/>时，我们可以保证BFGS更新的结果是正定的，从而保证<img src="https://latex.codecogs.com/svg.image?\Delta&space;x"/>的方向是函数值下降的方向，对凸函数而言，这是绝对成立的（可以回顾强凸性的定义）；非凸函数后面讨论
  - 适用于凸且光滑函数的BFGS方法的流程
    - ![BFGS for convex](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/unconstrained_optimization/bfgs_for_convex.png?raw=true)
    - <img src="https://latex.codecogs.com/svg.image?g"/>是梯度
    - <img src="https://latex.codecogs.com/svg.image?d"/>是更新方向
    - <img src="https://latex.codecogs.com/svg.image?t"/>是line search方法确定的步长
  - 缺点
    - 严格梯度单调性（严格凸函数）的条件过于苛刻，一般函数很难满足
    - 曲率的计算在optimum附近有效，在远处反而是浪费算力
    - 每次迭代的计算复杂度是优化变量的维度的平方，还是不够轻量
    - 在非凸函数上是否能够保证收敛仍未知
    - 在非光滑函数上能否正常使用仍未知