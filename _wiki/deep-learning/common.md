---
layout: wiki
title: Common Knowledges on Deep Learning
categories: [deep-learning]
description: wiki on Deep Learning
keywords: deep-learning
---

# 基础知识
- ### 极大似然估计
  - <img src="https://latex.codecogs.com/svg.image?p(x|\theta)"/>
  - 如果<img src="https://latex.codecogs.com/svg.image?\theta"/>是已知的，则该函数叫做概率函数(Probability)，描述的是不同样本点 出现的概率；
  - 如果<img src="https://latex.codecogs.com/svg.image?x"/>是已知的，则该函数叫做似然函数(Likelihood)，描述的是对于不同的模型参数，出现样本点<img src="https://latex.codecogs.com/svg.image?x"/>的概率。
- ### 反向传播
  - TODO
- ### 激活函数
  - 作用：对输入空间进行“弯曲（非线性变换）“，提升神经网络的非线性表达能力。
  - 设计原则
    - 非线性函数，且连续可导
    - 函数及其导函数易于计算
    - 导函数的值域以及分布
  - 典型函数
    - ![Activation Function](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/dl/common/activation_function.png?raw=true)
    - ![Activation Function Info.](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/dl/common/activation_function_info.png?raw=true)
      - Z字型下降
        - ![Z](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/dl/common/z.png?raw=true)
        - 如果输入神经元的数据总是正数（无论什么数值经过sigmoid之后都是正数），那么上图的<img src="https://latex.codecogs.com/svg.image?a_{i}^{(j)}"/>都是正数。梯度的符号取决于<img src="https://latex.codecogs.com/svg.image?\delta _{2}^{(3)}"/>，要么全部是正数，要么全部是负数。但很多时候，<img src="https://latex.codecogs.com/svg.image?\Theta _{ij}^{(l)}"/>的梯度更新方向是异号的，类似于右下角图片的黑色线段，同号就会导致更新需要花费很多步来回迭代到最优解（参数的运动路线像字母Z）