---
layout: wiki
title: Common Knowledges on Reinforcement Learning
categories: [reinforcement-learning]
description: wiki on Reinforcement Learning
keywords: reinforcement-learning
---

# 问题定义
```
每个时刻<img src="https://latex.codecogs.com/gif.latex?t"/>
- 智能体(Agent)
  - 智能体执行动作<img src="https://latex.codecogs.com/gif.latex?A_{t}"/>，并在环境中得到观测<img src="https://latex.codecogs.com/gif.latex?O_{t}"/>和奖励<img src="https://latex.codecogs.com/gif.latex?R_{t}"/>
- 环境(Environment)
  - 环境会对智能体的动作<img src="https://latex.codecogs.com/gif.latex?A_{t}"/>的做出反应,然后发送新的观测<img src="https://latex.codecogs.com/gif.latex?O_{t+1}"/>和奖励<img src="https://latex.codecogs.com/gif.latex?R_{t+1}"/>
```

# 核心概念
- ## 智能体
  - 智能体是指强化学习需要优化的部分,是我们能够精确控制的部分
- ## 环境
  - 环境是我们不能直接控制的部分
  - 环境并不是指自然环境，不同的问题,智能体和环境的划分也有所区别
    - 机器人探索房间 vs. 机器人行走控制
    - 仿真环境中的控制 vs. 实际环境中的控制
  - 区分智能体和环境是强化学习的第一步
- ## 奖励(Reward)
  - 奖励是强化学习的核心
  - 可以没有观测,但是不能没有奖励
  - 奖励是强化学习区别其他机器学习的标志特征
  - 特点
    - 奖励<img src="https://latex.codecogs.com/gif.latex?R_{t}"/>是一个**标量**反馈
    - 它衡量了智能体在时间<img src="https://latex.codecogs.com/gif.latex?t"/>上做得有多好
    - 智能体的目标就是**最大化累计奖励**

# 强化学习组成
- ## 奖励
> 指智能体在执行某个动作<img src="https://latex.codecogs.com/gif.latex?A_{t}"/>后得到的累计回报<img src="https://latex.codecogs.com/gif.latex?G_{t}"/>，<img src="https://latex.codecogs.com/gif.latex?G_{t}=w_{t}R_{t}+w_{t+1}R_{t+1}+w_{t+2}R_{t+2}+..."/>，<img src="https://latex.codecogs.com/gif.latex?w_{t+n}=\gamma ^{n}"/>。其中<img src="https://latex.codecogs.com/gif.latex?\gamma "/>越小表示我们越关注短期奖励，<img src="https://latex.codecogs.com/gif.latex?\gamma "/>越大表示我们越关注长期奖励

- ## 状态
  - ### 环境状态
    - 所有能够影响环境产生观测/奖励的数据都被认为是环境状态的一部分
    - 环境状态一般是智能体观察不到的
  - ### 智能体状态
    - 所有能够影响智能体做出下一个动作的数据都被认为是智能体状态的一部分
    - 一般情况下我们说的状态都是智能体状态
  - ### 全观测
    - 智能体能够直接观测到环境状态
    - 或者说智能体状态等价于环境状态
    - 这是强化学习的主要研究问题(马尔可夫决策过程)
  - ### 部分观测
    - 智能体不直接观测到环境状态
    - 智能体状态 ≠ 环境状态
    - 部分观测下的马尔可夫决策问题

- ## 动作
  - 动作是智能体主动和环境交互的媒介
  - 动作必须对环境起到一定的控制作用

- ## 智能体
  - ### 策略(Policy)
    - 从状态到动作的映射
    - 最终的目的就是找到一个策略
  - ### 值函数(value function)
    - 主要用来评价不同状态的好坏，指导动作的选择
  - ### 模型(model)
    - 模型指智能体所拥有的对环境的预测模型
