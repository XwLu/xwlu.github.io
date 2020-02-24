---
layout: wiki
title: Temporal Difference Lamda
categories: [reinforcement-learning]
description: wiki on Reinforcement Learning
keywords: reinforcement-learning, TD(λ)
---

# Temporal Difference
- 增量式MC
  - <img src="https://latex.codecogs.com/gif.latex?V(S_{t})=V(S_{t})+\frac{1}{N(S_{t})}(G_{t}-V(S_{t}))"/>
- 时间差分TD
  - <img src="https://latex.codecogs.com/gif.latex?V(S_{t})=V(S_{t})+\frac{1}{N(S_{t})}(R_{t+1}+\gamma V(S_{t+1})-V(S_{t}))"/>
- 核心差别：
  - MC是根据[s1→终止状态]完整片段的最终回报更新s1的值函数
  - TD是根据[s1→s2]这一步片段的即时回报值R和s2的估计值函数更新s1的值函数

# 与DP的对比
- DP是全宽备份
- TD是样本备份

# TD与MC的对比
- **TD 算法在知道结果之前学习**
  - TD算法在每一步之后都能在线学习
  - MC算法必须等待最终回报值得到之后才能学习
- **TD算法即便没有最终结果也能学习**
  - TD算法能够从不完整序列中学习
  - MC算法仅仅能够从完整序列中学习
  - TD算法适用于连续性任务和片段性任务
  - MC算法仅仅适用于片段性任务
- **TD算法有多个驱动力**
  - MC算法只有奖励值作为更新的驱动力
  - TD算法有奖励值和状态转移作为更新的驱动力
- **MC有高方差,零偏差**
  - 收敛性较好 (即使采用函数逼近)
  - 对初始值不太敏感
  - 随着样本数量的增加,方差逐渐减少, 趋近于 0
- **TD 有低方差,和一些偏差**
  - 通常比 MC 效率更高
  - 表格法下TD(0)收敛到<img src="https://latex.codecogs.com/gif.latex?V_{\pi }(s)"/>(函数逼近时不一定)
  - 对初始值更敏感
  - 随着样本数量的增加,偏差逐渐减少,趋近于 0
  - 样本数量有限时，TD的结果与真实结果的偏差比较稳定。MC可能出现巨大偏差。
- **TD要求环境符合马尔科夫性，MC不要求**

# 自举和采样
- **自举**: 使用随机变量的估计去更新
  - MC 没有自举
  - DP 和 TD 都有自举
- **采样**: 通过样本估计期望
  - MC 和 TD 采样
  - DP 不采样

# TD的优化方法
- 整体思路是 策略评价+策略提升
- ## 策略评价
  - ### 在策略评价**SARSA**
    - #### 公式
      - <img src="https://latex.codecogs.com/gif.latex?Q(S_{t},A_{t})\leftarrow Q(S_{t},A_{t})+\alpha (R_{t+1}+\gamma Q(S_{t+1},A_{t+1})-Q(S_{t},A_{t}))"/>
    - #### 算法流程
      -  1:初始化<img src="https://latex.codecogs.com/gif.latex?Q(s,a), \forall s\in S, a\in A(s)"/>且<img src="https://latex.codecogs.com/gif.latex?Q(S_{end},\cdot )=0"/>
      -  2:repeat(对于每个片段)
      -  3:  初始化状态<img src="https://latex.codecogs.com/gif.latex?S"/>
      -  4:  根据<img src="https://latex.codecogs.com/gif.latex?Q"/>选择一个在<img src="https://latex.codecogs.com/gif.latex?S"/>处的动作<img src="https://latex.codecogs.com/gif.latex?A"/>(使用<img src="https://latex.codecogs.com/gif.latex?\varepsilon "/>-贪婪策略)
      -  5:  repeat(对于片段中每一步)
      -  6:    执行动作<img src="https://latex.codecogs.com/gif.latex?A"/>，观测<img src="https://latex.codecogs.com/gif.latex?R,S^{'}"/>
      -  7:    根据<img src="https://latex.codecogs.com/gif.latex?Q"/>选择一个在<img src="https://latex.codecogs.com/gif.latex?S^{'}"/>处的动作<img src="https://latex.codecogs.com/gif.latex?A^{'}"/>(使用<img src="https://latex.codecogs.com/gif.latex?\varepsilon "/>-贪婪策略)
      -  8:    <img src="https://latex.codecogs.com/gif.latex?Q(S_{t},A_{t})\leftarrow Q(S_{t},A_{t})+\alpha (R_{t+1}+\gamma Q(S_{t+1},A_{t+1})-Q(S_{t},A_{t}))"/>
      -  9:    <img src="https://latex.codecogs.com/gif.latex?S\leftarrow S^{'};A\leftarrow A^{'}"/>
      - 10:  until <img src="https://latex.codecogs.com/gif.latex?S"/>是终止状态
      - 11:until收敛
    - #### 收敛性
      - 在满足以下条件时,Sarsa 算法收敛到最优的状态动作值函数
        - 策略序列<img src="https://latex.codecogs.com/gif.latex?\pi _{t}(a|s)"/>满足GLIE
        - 步长序列<img src="https://latex.codecogs.com/gif.latex?\alpha _{t}"/>是一个Robbins-Monro序列
          - <img src="https://latex.codecogs.com/gif.latex?\sum_{t=1}^{\infty }\alpha _{t}=\infty ,\sum_{t=1}^{\infty }\alpha _{t}^{2}=\infty "/>
      - GLIE 保证了
        - 充分的探索
        - 策略最终收敛到贪婪的策略
      - Robbins-Monro保证了
        - 步长足够大,足以克服任意初始值
        - 步长足够小,最终收敛 (常量步长不满足)
  - ### 期望SARSA
    - <img src="https://latex.codecogs.com/gif.latex?Q(S_{t},A_{t})\leftarrow Q(S_{t},A_{t})+\alpha (R_{t+1}+\gamma \sum_{a}\pi (a|S_{t+1})Q(S_{t+1},a)-Q(S_{t},A_{t}))"/>
    - 减少了由于<img src="https://latex.codecogs.com/gif.latex?A^{'}"/>的选择带来的方差
    - 在相同更新步数时,期望 Sarsa 比 Sarsa 的通用性更好
    - 可以在在策略和离策略中切换
      - 在策略:TD目标值中的<img src="https://latex.codecogs.com/gif.latex?R_{t+1}+\gamma \sum_{a}\pi (a|S_{t+1})Q(S_{t+1},a)"/>中的策略<img src="https://latex.codecogs.com/gif.latex?\pi "/>和采样的策略是同一个策略
      - 离策略:TD目标值中的<img src="https://latex.codecogs.com/gif.latex?R_{t+1}+\gamma \sum_{a}\pi (a|S_{t+1})Q(S_{t+1},a)"/>中的策略<img src="https://latex.codecogs.com/gif.latex?\pi "/>和采样的策略是不同的策略
    - 一种特殊情况,TD目标值中的策略选择贪婪策略, 采样的策略选用ε-贪婪策略——**Q学习**
  - ### 离策略评价**Q学习**
    - #### 公式
      - <img src="https://latex.codecogs.com/gif.latex?Q(S,A)\leftarrow Q(S,A)+\alpha (R+\gamma \max_{a^{'}}Q(S^{'},a^{'})-Q(S,A))"/>
    - #### 算法流程
      -  1:初始化<img src="https://latex.codecogs.com/gif.latex?Q(s,a), \forall s\in S, a\in A(s)"/>且<img src="https://latex.codecogs.com/gif.latex?Q(S_{end},\cdot )=0"/>
      -  2:repeat(对于每个片段)
      -  3:  初始化状态<img src="https://latex.codecogs.com/gif.latex?S"/>
      -  4:  repeat(对于片段中每一步)
      -  5:    根据<img src="https://latex.codecogs.com/gif.latex?Q"/>选择一个在<img src="https://latex.codecogs.com/gif.latex?S"/>处的动作<img src="https://latex.codecogs.com/gif.latex?A"/>(使用<img src="https://latex.codecogs.com/gif.latex?\varepsilon "/>-贪婪策略)
      -  6:    执行动作<img src="https://latex.codecogs.com/gif.latex?A"/>，观测<img src="https://latex.codecogs.com/gif.latex?R,S^{'}"/>
      -  7:    <img src="https://latex.codecogs.com/gif.latex?Q(S,A)\leftarrow Q(S,A)+\alpha (R+\gamma \max_{a^{'}}Q(S^{'},a^{'})-Q(S,A))"/>
      -  8:    <img src="https://latex.codecogs.com/gif.latex?S\leftarrow S^{'}"/>
      -  9:  until <img src="https://latex.codecogs.com/gif.latex?S"/>是终止状态
      - 10:until收敛
- ## 策略提升
  - ## <img src="https://latex.codecogs.com/gif.latex?\varepsilon "/>-贪婪策略提升
