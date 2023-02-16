---
layout: wiki
title: Smoothing Techniques
categories: [optimization]
description: smoothing techniques
keywords: optimization 
---

# Inf Convolution
- 用途
  - 对凸的非光滑的函数进行光滑化的方法
- 定义
  - ![definition](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/techniques/inf_conv.png?raw=true)
- 几何含义
  - ![definition](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/techniques/inf_conv_geometry.png?raw=true)

# Moreau envelope
- 定义
  - 是Inf Convolution的一个特例，是将被卷积的函数取二次型
  - ![definition](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/techniques/moreau_envelope.png?raw=true)
  - 增广拉格朗日的增广项本质上就是用这个方法做smoothing
  - <img src="https://latex.codecogs.com/svg.image?\gamma"/>是光滑度的调节参数
    - ![gamma](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/optimization/techniques/gamma_influence.png?raw=true)
- 性质
  - 原函数和Moreau envelope操作后得到的光滑函数的minima是同一个点