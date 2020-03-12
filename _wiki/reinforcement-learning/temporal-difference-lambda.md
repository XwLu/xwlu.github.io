---
layout: wiki
title: Temporal Difference Lambda
categories: [reinforcement-learning]
description: wiki on Reinforcement Learning
keywords: reinforcement-learning, TD(λ)
---

# Temporal Difference Lambda
- 时间差分就是TD(0)算法，只向后采样一步；MC是向后采样整个片段；多步自举介于两者之间
  - TD: <img src="https://latex.codecogs.com/gif.latex?n=1,G_{t}^{(1)}=R_{t+1}+\gamma V(S_{t+1})"/>
  - <img src="https://latex.codecogs.com/gif.latex?n=2,G_{t}^{(2)}=R_{t+1}+\gamma V(S_{t+1})"/>
  - MC: <img src="https://latex.codecogs.com/gif.latex?n=3,G_{t}^{(\infty )}=R_{t+1}+\gamma R_{t+2}+...+\gamma ^{T-t-1}R_{T}"/>
- 经验认为<img src="https://latex.codecogs.com/gif.latex?n=3-10"/>步左右，要好于TD(0)和MC

# n步TD策略评价
- ## 算法流程
  -  1:repeat(对于每一个片段)
  -  2:  repeat对于片段中的每一步
  -  3:    根据<img src="https://latex.codecogs.com/gif.latex?\pi (\cdot ,S_{t})"/>选择动作<img src="https://latex.codecogs.com/gif.latex?A_{t}"/>
  -  4:    执行动作<img src="https://latex.codecogs.com/gif.latex?A_{t}"/>，观察到<img src="https://latex.codecogs.com/gif.latex?R_{t+1}, S_{t+1}"/>，并将其存储起来
  -  5:    if <img src="https://latex.codecogs.com/gif.latex?\tau =t-n+1\geqslant 0"/>, then
  -  6:      <img src="https://latex.codecogs.com/gif.latex?G\leftarrow \sum_{i=\tau +1}^{min(\tau +n,T)}\gamma ^{i-\tau -1}R_{i}"/>
  -  7:      if <img src="https://latex.codecogs.com/gif.latex?\tau +n<T"/>,then <img src="https://latex.codecogs.com/gif.latex?G\leftarrow G+\gamma ^{n}V(S_{\tau +n})"/>
  -  8:      <img src="https://latex.codecogs.com/gif.latex?V(S_{\tau })\leftarrow V(S_{\tau })+\alpha [G-V(S_{\tau })]"/>
  -  9:    end if
  - 10:  until直到终止状态
  - 11:until收敛

- ## 两个注意点
  - 为了计算 n 步回报值,需要维护 R, S 的存储空间
  - 对于后继状态不足 n 个的,使用 MC 目标值

# 多步自举
## 前向视角
- 就是将TD(0),TD(1),...,TD(n)求平均
- <img src="https://latex.codecogs.com/gif.latex?G_{t}^{\lambda }=(1-\lambda )\sum_{n=1}^{\infty }\lambda ^{n-1}G_{t}^{(n)}"/>
- <img src="https://latex.codecogs.com/gif.latex?\lambda =0"/>，退化成TD(0)；<img src="https://latex.codecogs.com/gif.latex?\lambda =1"/>，退化成MC
- <img src="https://latex.codecogs.com/gif.latex?TD(\lambda )"/>更新公式
  - <img src="https://latex.codecogs.com/gif.latex?V(S_{t})\leftarrow V(S_{t})+\alpha (G_{t}^{\lambda }-V(S_{t}))"/>

## 后向视角--基于资格迹(Eligibility Traces)
- ### 基本概念
  - 状态转移片段：s1→s1→s1→s2→s3
  - 信度分配(Credit assignment)问题:到底是s1还是s2造成了最后的s3
    - 频率启发式: 归因到频数最高的状态
    - 近因启发式: 归因到最近的状态
    - 资格迹是两者的结合
- ### 资格迹的计算公式
  - <img src="https://latex.codecogs.com/gif.latex?E_{0}(s)=0"/>
  - <img src="https://latex.codecogs.com/gif.latex?E_{t}(s)=\gamma \lambda  E_{t-1}(s)+1(S_{t}=s)"/>
  - 直观的感觉就是，第一次遇到这个状态s的时候，对它的记忆由0蹦到1，然后慢慢开始遗忘(对应<img src="https://latex.codecogs.com/gif.latex?\times \gamma "/>这个动作)，下一次又遇到了，记忆就一下子清晰了(对应+1这个动作)，然后又慢慢遗忘。
- ### 利用资格迹实现多步自举
  - 对于每一个状态s，维护一个资格迹<img src="https://latex.codecogs.com/gif.latex?E(s)"/>
  - 更新值函数V(s)时，会更新每一个状态s
  - 使用TD误差<img src="https://latex.codecogs.com/gif.latex?\delta _{t}"/>和资格迹<img src="https://latex.codecogs.com/gif.latex?E_{t}(s)"/>
    - <img src="https://latex.codecogs.com/gif.latex?\delta _{t}=R_{t+1}+\gamma V(S_{t+1})-V(S_{t})"/>
    - <img src="https://latex.codecogs.com/gif.latex?V(s)\leftarrow V(s)+\alpha \delta _{t}E_{t}(s)"/>
  - 资格迹本质上是记录了所有状态s对后继状态<img src="https://latex.codecogs.com/gif.latex?S_{t+1}"/>的贡献度，被用来对TD误差进行加权

  ## 总结
  - 前向视角
    - 利用t+1,t+2,...t+n时刻的V(s)求解t时刻的V(s)
    - 容易理解
    - 需要拥有完整的状态转移片段才能求解，跟MC一样离线更新
  - 后向视角
    - 利用t+1时刻的V(s)更新t,t-1,t-2,t-3,...0时刻的V(s)
    - 在线更新，每一个时刻都更新一遍之前所有时刻的V

# n步Sarsa