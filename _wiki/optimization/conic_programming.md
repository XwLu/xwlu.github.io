---
layout: wiki
title: Conic Programming 
categories: [optimization]
description: conic programming 
keywords: optimization 
---

# 锥的定义
- ![cone geometry](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/conic_programming/cone_geometry.png?raw=true)
- 常见的几种Cone
  - ![cone geometry](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/conic_programming/common_cones.png?raw=true)
  - Nonnegative Orthant通过仿射变换可以变成线性规划LP
    - ![nonnegative cone](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/conic_programming/nonnegative_cone2lp.png?raw=true)
  - Second Order Cones
    - ![second order cone](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/conic_programming/nonnegative_cone2lp.png?raw=true)
  - Rotatted Second Order Cones(本质上是个反射变换，不是旋转)
    - ![rotated second order cone](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/conic_programming/rotated_second_order_cone.png?raw=true)
  - Symmetric Cones
    - 定义
      - 一个锥是对称锥，当且仅当其可以表达为一个平方操作<img src="https://latex.codecogs.com/svg.image?\left\{x^{2}:=x\circ%20x|x\in\mathbb{R}^{n}|\right\}"/>
      - 其中，<img src="https://latex.codecogs.com/svg.image?\circ/">操作需要满足如下性质
        - <img src="https://latex.codecogs.com/svg.image?x\circ%20y"/>是bilinear的
        - <img src="https://latex.codecogs.com/svg.image?x\circ%20y=y\circ%20x"/>
        - <img src="https://latex.codecogs.com/svg.image?x^{2}\circ(y\circ%20x)=(x^{2}\circ%20y)\circ%20x"/>
        - <img src="https://latex.codecogs.com/svg.image?\left%3Cx,y\circ%20z\right%3E=\left%3Cx\circ%20y,z\right%3E"/>
        - <img src="https://latex.codecogs.com/svg.image?(\mathbb{R}^{n},\circ)"/>所表示的这样一个集合被称为Euclidean Jordan algebra
    - 上面三种锥都是对称锥(因为都可以转化为平方的形式)
      - ![symmetric cone](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/conic_programming/symmetric_cone.png?raw=true)
      - 具体来说，上面三种对称锥，各自的<img src="https://latex.codecogs.com/svg.image?\circ/">操作的定义也是不一样的
        - ![circle operate definition](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/conic_programming/circle_operate_definition.png?raw=true)

# Spectral Decomposition
- 每个Euclidean Jordan algebra都有它的谱分解<img src="https://latex.codecogs.com/svg.image?x=\sum_{i=1}^{\theta}\lambda_{i}q_{i}"/>，其中，<img src="https://latex.codecogs.com/svg.image?\lambda_{i}"/>是特征值，<img src="https://latex.codecogs.com/svg.image?q_{i}"/>是特征向量
- Euclidean Jordan algebra的谱分解具有如下性质
  - <img src="https://latex.codecogs.com/svg.image?q_{i}^{2}=q_{i}"/>，<img src="https://latex.codecogs.com/svg.image?q_{i}\circ%20q_{j\neq%20i}=0"/>
