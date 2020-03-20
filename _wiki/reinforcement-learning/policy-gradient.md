---
layout: wiki
title: Policy Gradient
categories: [reinforcement-learning]
description: wiki on Reinforcement Learning
keywords: reinforcement-learning, PG
---

# 强化学习分类
- ## 策略梯度算法
  - 直接用神经网络表示策略
  - 神经网络输出N维的向量，每一维表示选择该动作的概率大小
  - <img src="https://latex.codecogs.com/gif.latex?Net(state)=[P_{action1},P_{action2},P_{action3},...]"/>
- ## 值函数算法
  - 用神经网络拟合Q或者V函数
  - 得到Q之后，利用贪婪策略等选择下一步动作
  - <img src="https://latex.codecogs.com/gif.latex?Net(state,action)=Q"/>或者<img src="https://latex.codecogs.com/gif.latex?Net(state)=V"/>
- ## Actor-Critic
  - 学习值函数
  - 学习策略
  - 介于上面两者之间

# 策略梯度算法优缺点
- ## 优点
  - 更好的收敛性
  - 有效处理高维和连续的动作空间
  - 能够学到随机策略
  - 不会导致策略退化
- ## 缺点
  - 容易陷入局部最优
  - 难以评价一个策略，评价结果方差很大

# 策略退化
- 模型的能力不够导致
- 值函数估计不准导致

# 优化目标
- <img src="https://latex.codecogs.com/gif.latex?max_{\theta }U(\theta )=max\sum_{\tau }P(\tau |\theta )R(\tau )"/>
- 其中，<img src="https://latex.codecogs.com/gif.latex?\theta "/>表示策略网络的参数；<img src="https://latex.codecogs.com/gif.latex?\tau "/>表示一段状态转移轨迹；<img src="https://latex.codecogs.com/gif.latex?R(\tau ) "/>表示该轨迹的最终回报值；<img src="https://latex.codecogs.com/gif.latex?P(\tau |\theta )"/>表示当策略网络的参数为<img src="https://latex.codecogs.com/gif.latex?\theta "/>时，出现<img src="https://latex.codecogs.com/gif.latex?\tau "/>的概率大小。
- 在一个固定的环境再，一般来说，<img src="https://latex.codecogs.com/gif.latex?R(\tau ) "/>是稳定不变的。

# 优化方法
- ## 梯度表达式
  - <img src="https://latex.codecogs.com/gif.latex?\frac{\partial U(\theta )}{\partial \theta }=\frac{\partial \sum_{\tau }P(\tau |\theta )R(\tau )}{\partial \theta }"/>
- ## 似然率角度梯度求解
  - ![LIKEHOOD](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/rl/pg/likehood-solver.png?raw=true)
- ## 似然率梯度的理解
  - <img src="https://latex.codecogs.com/gif.latex?\frac{\partial logP(\tau |\theta )}{\partial \theta }"/>是轨迹<img src="https://latex.codecogs.com/gif.latex?\tau "/>的出现概率随<img src="https://latex.codecogs.com/gif.latex?\theta "/>变化最陡的方向。
    - 沿正方向，轨迹出现的概率会变大
    - 沿负方向，轨迹出现的概率会变小
  - <img src="https://latex.codecogs.com/gif.latex?R(\tau )"/>控制了参数更新的方向和步长，R是正的，就让轨迹出现的概率变大，并且R越大，步长的幅度越大；相反亦然。
  - 最终增大了高回报率轨迹出现的概率，减少了低回报率轨迹出现的概率
- ## 轨迹分解到状态
  - ![decompose](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/rl/pg/decompose.png?raw=true)
- ## 算法流程
  - ![reinforce](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/rl/pg/reinforce.png?raw=true)

# Actor-Critic
- 上面的reinforce算法中，<img src="https://latex.codecogs.com/gif.latex?g_{t}"/>的方差非常大，为了减小方差，我们引入了Critic函数<img src="https://latex.codecogs.com/gif.latex?Q_{w}(s_{k},a_{k})\approx \sum_{t=k}^{T}\gamma ^{t-k}R(s_{k},a_{k})"/>代替<img src="https://latex.codecogs.com/gif.latex?g_{t}"/>
- 再进一步，由于每个Q都是正的，会导致网络对于任何轨迹都想提高其出现的概率，因此，引入一个基线。基线的选择为当前状态的V值。由此得到一个优势函数：
- <img src="https://latex.codecogs.com/gif.latex?A^{\pi _{\theta }}(s,a)=Q^{\pi _{\theta }}(s,a)-V^{\pi _{\theta }}(s)"/>
- 上面的方法需要设计一个Q函数一个V函数，为了简化，我们直接用TD误差代替优势函数。TD误差为：
- <img src="https://latex.codecogs.com/gif.latex?\delta ^{\pi _{\theta }}=r+V^{\pi _{\theta }}({s}')-V^{\pi _{\theta }}(s)"/>其中，<img src="https://latex.codecogs.com/gif.latex?{s}' "/>是<img src="https://latex.codecogs.com/gif.latex?s"/>的后一个状态

# 总结
- ![reinforce](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/rl/pg/conclusion.png?raw=true)
- 其中，Advantage Actor-Critic又叫A2C，由于TD Actor-Critic是Advantage Actor-Critic的无偏估计，所以实际在使用A2C的时候，都是用的TD Actor-Critic
- ![A2C](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/rl/pg/a2c.png?raw=true)
- A2C需要多进程来打破训练数据之间的相关性

# 扩展(其他策略梯度算法)
- 自然梯度法：寻找策略更新最快的方向
- 信赖域策略优化算法(TRPO)：研究了更新步长的选择，步长选择在策略梯度中非常重要，但实现非常复杂
- 近端策略优化(PPO)：对TRPO的改进，使实现非常简单，实际使用中，效果比较好甚至最好的方案
- 确定性策略梯度算法(DPG)：输出是确定性动作，而不是概率