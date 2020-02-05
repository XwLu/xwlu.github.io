---
layout: wiki
title: Markov Decision Processes
categories: [reinforcement-learning]
description: wiki on Markov Decision Processes
keywords: reinforcement-learning, MDP
---

# MDPs
- ## 马尔可夫性
  - 只要知道现在，将来和过去条件独立
  - 每一时刻的状态只与上一时刻的状态有关
  - 当前状态包含了所有的历史状态信息
  - 要求环境全观测

- ## 任务类型定义
  - 强化学习中,从初始状态<img src="https://latex.codecogs.com/gif.latex?S_{1}"/>到终止状态的序列过程,被称为一个片段(episode)。
    - 如果一个任务总以终止状态结束,那么这个任务被称为片段任务(episodic task)
    - 如果一个任务会没有终止状态,会被无限执行下去,这被称为连续性任务 (continuing task)
  - 终止状态等价于自身转移概率为 1,奖励为 0 的的状态

- ## 状态转移矩阵

s1 | s2 | s3 | s4 | 转移
-|-|-|-|-
0.5 | 0.0 | 0.5 | 0.0 | s1 |
0.1 | 0.2 | 0.3 | 0.4 | s2 |
0.0 | 0.0 | 0.0 | 1.0 | s3 |
0.0 | 0.0 | 0.0 | 1.0 | s4 |

> 上图中s1转换到s1的概率是0.5，转换到s3的概率是0.5；s2转换到s1的概率是0.1，转换到s2的概率是0.2，转换到s3的概率是0.3，转换到s4的概率是0.4。

- ## 奖励与回报
  - ### 奖励值:对每一个状态的评价
  - ### 回报值: 对每一个片段的评价
    - 对于片断性任务，回报值是未来有限个状态的奖励值的和<img src="https://latex.codecogs.com/gif.latex?G_{t}=\sum_{k=0}^{T-t-1}\gamma ^{k}R_{t+k+1}"/>
    - 对于连续性任务，回报值是未来无限个状态的奖励值的和<img src="https://latex.codecogs.com/gif.latex?G_{t}=\sum_{k=0}^{\infty }\gamma ^{k}R_{t+k+1}"/>
    - 回报值是从时间<img src="https://latex.codecogs.com/gif.latex?t"/>处开始的累计衰减奖励
  - ### 指数衰减值
    - 对未来的把握也是逐渐衰减的
    - 一般情况下,我们更关注短时间的反馈
  - ### 值函数:某个状态所对应回报值的期望

- ## 贝尔曼方程
  - 强化学习的核心
  - <img src="https://latex.codecogs.com/gif.latex?v(s)=R(s)+\gamma \sum_{s'}^{ }P_{s{s}'}v({s}')"/>

- ## 策略
  - 状态值函数(<img src="https://latex.codecogs.com/gif.latex?v_{\pi }(s)"/>)：是从状态 s 开始，使用策略 π 得到的期望回报值
  - 状态动作值函数(<img src="https://latex.codecogs.com/gif.latex?q_{\pi }(s,a)"/>)：是从状态 s 开始,执行动作 a，然后使用策略 π 得到的期望回报值
  - <img src="https://latex.codecogs.com/gif.latex?v_{\pi }(s)=\sum \pi (a|s)q_{\pi }(s,a)"/>
  - <img src="https://latex.codecogs.com/gif.latex?q_{\pi }(s,a)=R(s,a)+\gamma \sum_{s'\in S}^{ }P_{s{s}'}^{a}v_{\pi }(s)"/>

- ## 知识点
  - 贝尔曼最优方程不是线性的
  - 一般很难有闭式的解
  - 可以使用迭代优化的方法去解
    - 值迭代
    - 策略迭代
    - Q 学习
    - SARSA

---

# POMDP
- 观测不等于状态O ≠ S
- POMDPs 由七元组构成 < S, A, O, P, R, Z, γ >
- Z是观测函数
- 观测不满足马尔可夫性,因此也不满足贝尔曼方程
- 状态未知,隐马尔可夫过程
- 有时对于 POMDPs 来说,最优的策略是随机性的

# 无衰减 MDPs
- 用于各态历经马尔可夫决策过程
- 存在独立于状态的平均奖赏
- 求值函数时,需要减去该平均奖赏,否则有可能奖赏爆炸