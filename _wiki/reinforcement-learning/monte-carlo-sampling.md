---
layout: wiki
title: Monte-Carlo Sampling
categories: [reinforcement-learning]
description: wiki on Reinforcement Learning
keywords: reinforcement-learning, mc-sampling
---

# 基本概念
- 蒙特卡洛采样是无模型方法
- 行为策略是智能体与环境交互的策略
- 目标策略是我们要学习的策略
- ## 在策略（on-policy）学习
  - 行为策略和目标策略是同一个策略
  - 直接使用样本统计属性去估计总体
  - 更简单,且收敛性更好
  - 数据利用性更差(只有智能体当前交互的样本能够被利用)
  - 限定了学习过程中的策略是随机性策略
- ## 离策略（off-policy）学习
  - 行为策略和目标策略不是同一个策略
  - 一般行为策略选用随机性策略，目标策略选用确定性策略
  - 需要结合重要性采样才能使用样本估计总体
  - 方差更大,收敛性更差
  - 数据利用性更好 (可以使用其他智能体交互的样本)
  - 行为策略需要比目标策略更具备探索性
- ## 重要性采样
  - 是一种估计概率分布期望值的技术,它使用了来自其他概率分布的样本
  - 主要用于无法直接采样原分布的情况
  - 估计期望值时,需要加权概率分布的比值

# 算法特性
- MC方法可以被用于任意涉及随机变量的估计
- 这里MC方法特指利用统计平均估计期望值的方法
- MC方法从完整的片段中学习
- MC方法仅仅用于片段性任务(必须有终止条件)

# 算法核心
> 通过不断的采样,然后统计平均回报值来估计值函数,方差较大
- 从某个状态S开始，通过某种策略P进行探索，一直到终止状态，得到反馈Fk
- 重复以上步骤n次，V(s)=(F1+F2+...+Fn)/n

# 蒙特卡洛评价
- 首次拜访(First-visit)MC策略评价
- 每次拜访(Every-visit)MC策略评价
```
s1,s2,s3,s1,s4,s2,s5 +1
s1,s2,s1,s5 +1
对于上面的两种采样轨迹，评价s1时，首次拜访只在s1在一条轨迹中第一次出现时N=N+1；每次拜访则是出现一次s1就N=N+1
首次拜访：(1+1)/2 = 1
每次拜访：(1+1)/4 = 0.25
```

# Q函数的MC方法
- 每次是针对一个s和一个a进行评价
- 为了充分探索所有的s,a组合，随机选择初始状态和初始动作

# 离策略的MC策略评价
- 核心是利用重要性采样去加权回报值
- <img src="https://latex.codecogs.com/svg.image?G_{t}^{\pi /\mu }=\prod_{k=t}^{T-1} \frac{\pi (A_{k}|S_{k}))}{\mu (A_{k}|S_{k})}G_{t}"/>
- 使用重要性采样会显著增加方差, 可能到无限大

# MC小结
- 偏差为 0,是无偏估计
- 方差较大,需要大量数据去消除
- 收敛性较好
- 没有利用马尔可夫性,有时可以用在非马尔可夫环境

---

# 增量式MC
> 之前的蒙特卡洛算法需要采样大量轨迹之后再统一计算平均数，能不能在每一条轨迹之后都得到值函数的估计值呢?
- <img src="https://latex.codecogs.com/svg.image?N(S_{t})=N(S_{t})+1"/>
- <img src="https://latex.codecogs.com/svg.image?V(S_{t})=V(S_{t})+\frac{1}{N(S_{t})}(G_{t}-V(S_{t}))"/>
- 这里的<img src="https://latex.codecogs.com/svg.image?N(S_{t})"/>可以认为是更新的步长
- 很多时候，我们会把<img src="https://latex.codecogs.com/svg.image?N(S_{t})"/>替换为一个常数<img src="https://latex.codecogs.com/svg.image?\alpha "/>，好处如下：
  - 会逐渐遗忘过去的轨迹
  - 对初始值敏感度更小
  - 适用于不稳定环境

# MC策略提升
- 不能使用贪婪策略提升，会导致部分状态永远不会遍历到
- 每次探索，有一定的几率随机选择动作，其他情况下都采取贪婪策略

# 无限探索下的极限贪婪(GLIE)
- 无限探索:所有的状态动作对能够被探索无穷次
- 极限贪婪:在极限的情况下,策略会收敛到一个贪婪的策略
> GLIE 蒙特卡洛优化能收敛到最优的 Q 函数

# 增量式离策略每次拜访蒙特卡洛评价

- 1: repeat <img src="https://latex.codecogs.com/svg.image?k=1,2,3,..."/>
- 2:     使用策略<img src="https://latex.codecogs.com/svg.image?\mu "/>采样第<img src="https://latex.codecogs.com/svg.image?k"/>条轨迹，<img src="https://latex.codecogs.com/svg.image?S_{1},A_{1},S_{2},A_{2},...,S_{T}"/>
- 3:     <img src="https://latex.codecogs.com/svg.image?G\leftarrow 0, W\leftarrow 1"/>
- 4:     for <img src="https://latex.codecogs.com/svg.image?t=T-1,T-2,...,0"/> do
- 5:         <img src="https://latex.codecogs.com/svg.image?G\leftarrow \gamma G+R_{t+1}"/>
- 6:         <img src="https://latex.codecogs.com/svg.image?C(S_{t},A_{t})\leftarrow C(S_{t},A_{t})+W"/>
- 7:         <img src="https://latex.codecogs.com/svg.image?Q(S_{t},A_{t})\leftarrow Q(S_{t},A_{t})+\frac{W}{C(S_{t},A_{t})}[G-Q(S_{t},A_{t})]"/>
- 8:         <img src="https://latex.codecogs.com/svg.image?W\leftarrow W\frac{\pi (A_{t}|S_{t})}{\mu (A_{t}|S_{t})}"/>
- 9:         if W=0, break
- 10:    end for
- 11: until 收敛

# 增量式离策略每次拜访蒙特卡洛优化

- 1: repeat <img src="https://latex.codecogs.com/svg.image?k=1,2,3,..."/>
- 2:     使用策略<img src="https://latex.codecogs.com/svg.image?\mu "/>采样第<img src="https://latex.codecogs.com/svg.image?k"/>条轨迹，<img src="https://latex.codecogs.com/svg.image?S_{1},A_{1},S_{2},A_{2},...,S_{T}"/>
- 3:     <img src="https://latex.codecogs.com/svg.image?G\leftarrow 0, W\leftarrow 1"/>
- 4:     for <img src="https://latex.codecogs.com/svg.image?t=T-1,T-2,...,0"/> do
- 5:         <img src="https://latex.codecogs.com/svg.image?G\leftarrow \gamma G+R_{t+1}"/>
- 6:         <img src="https://latex.codecogs.com/svg.image?C(S_{t},A_{t})\leftarrow C(S_{t},A_{t})+W"/>
- 7:         <img src="https://latex.codecogs.com/svg.image?Q(S_{t},A_{t})\leftarrow Q(S_{t},A_{t})+\frac{W}{C(S_{t},A_{t})}[G-Q(S_{t},A_{t})]"/>
- 8:         <img src="https://latex.codecogs.com/svg.image?\pi (S_{t})\leftarrow argmax_{a}Q(S_{t},a)"/>
- 9:         if <img src="https://latex.codecogs.com/svg.image?A_{t}\neq \pi (S_{t})"/>，则退出for循环
- 10:       <img src="https://latex.codecogs.com/svg.image?W\leftarrow W\frac{1}{\mu (A_{t}|S_{t})}"/>
- 11:    end for
- 12: until 收敛
