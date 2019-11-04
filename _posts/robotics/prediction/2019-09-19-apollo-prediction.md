---
layout: post
title: Apollo Predcition
categories: [Robotics]
description: 阅读apollo prediction笔记
keywords: apollo, prediction
---

## 前言
目前用python重写了Apollo的Prediction模块，对Apollo3.5版本的prediction进行一个总结。

---

## 数据结构
#### 最核心的数据结构就是unordered_map<Id, queue<Feature>> obstacles，存储了所有obtscale的固定长度的历史特征。

- Feature
  - position
  - timestamp
  - type
  - velocity
  - acceleration
  - length
  - width
  - height
  - is_near_junction
  - junction_feature
  - is_still
  - priority
  - tracking_status
  - lanes_feature 道路特征
    - lane_feature 最可能是当前车道的车道特征
    - current_lanes_feature 所有可能是当前车道的车道特征
    - nearby_lanes_feature 周围车道的车道特征
    - lane_graph 存储lane_sequence的vector
      - lane_sequence 其中一种可行的车道序列

## 结构
- 主体结构分为：Container-->Evaluator-->Predictor
- Container = PoseContainer + ADCTrajectoryContainer + ObstacleContainer
  - PoseContainer保存了自身车辆当前的状态信息
  - ADCTrajectoryContainer保存了自身车辆的规划轨迹
  - ObstacleContainer对感知结果进行特征抽取与历史保存
- Evaluator = LaneSequenceEvaluator + JunctionEvaluator + ...
  - LaneSequenceEvaluator对当前车辆的候选sequence进行概率评估
  - JunctionEvaluator对车辆所处路口的出口选择进行概率评估
- Predictor = LaneSequencePredictor + JunctionPredictor + FreeMovePredictor + ...
  - LaneSequencePredictor选取概率最高的sequence来生成轨迹
  - JunctionPredictor选取概率最高的路口出口来生成轨迹
  - FreeMovePredictor根据运动学模型推算

## 代码流程
- Prediction的主要执行流程在prediction/common/message_process.cc文件中
- 大概的流程框架如下
  - 初始化
    - prediction_map初始化
    - obstacle_clusters初始化
    - junction_analyzer初始化
    - obstacle_container初始化
    - obstacles_prioritizer初始化
    - pose_container初始化
    - scenario_manager初始化
    - adc_trajectory_container初始化
    - predictor_manager初始化
    - evaluator_manager初始化
  - 感知结果回调函数
    - 将感知到的obstacles塞到obstacle_container里面，进行特征抽取，并存储在历史队列中
    - 对obstacles赋值优先级-->是否ignore
    - 运行scenrio_manager，抽取目标obstacle周围的环境特征并分析
    - 如果当前环境是路口环境，构建junction feature
    - build lane graph
    - 运行evaluator_manager
    - 运行predictor_manager
    - 发布预测轨迹

## 核心思想总结
- 车辆在结构化道路场景下
  - 针对每个obstacle，根据其最新一帧的数据计算所有可能的lane sequence，并提取sequence的特征。
  - 针对每条sequence，根据其过去N帧历史数据，评估其被选择为目标sequence的概率。
  - 选择概率最高的sequence，生成轨迹。
- 车辆在路口场景下
  - 将上文中的sequence换成路口的出口，判断obstacle最有可能从哪个出口出路口，生产对应轨迹。
- 车辆在open space场景下
  - free move
- 行人
  - social lstm
