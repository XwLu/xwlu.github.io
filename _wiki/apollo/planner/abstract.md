# Apollo

## common

### path
- DiscretizedPath:
  - 构造vector<PathPoint>类型的path points。
  - 输入一个s，根据离散的path points插值出对应的PathPoint类型的点。

- FrenetFramePath:
  - 构造vector<FrenetFramePoint>类型的points。
  - 输入一个s，根据离散的points插值出对应的PathPoint类型的点。

- PathData:
  - PathPoint的类型：IN_LANE,OUT_ON_FORWARD_LANE,OUT_ON_REVERSE_LANE,OFF_ROAD,UNKNOWN。
  - 设置DiscretizedPath
    - 必须先设置reference line。
    - 将DiscretizedPath转换为FrenetFramePath。
    - 将DiscretizedPath和FrenetFramePath存入到HistoryPath。
  - 设置FrenetFramePath
    - 必须先设置reference line。
    - 将FrenetFramePath转换为DiscretizedPath。
    - 将DiscretizedPath和FrenetFramePath存入到HistoryPath。
  - 设置ReferenceLine
  - 设置PathPointDecisionGuide
    - vector<tuple<double, PathPointType, double> >。
    - 必须先设置reference line。
    - 必须先设置DiscretizedPath和FrenetFramePath。
  - 根据pathS获取DiscretizedPath上的点
  - 根据RefS获取DiscretizedPath上的点
  - 将XY坐标转换为SL坐标
  - 将SL坐标转换为XY坐标
  - 输入一个FrenetFramePoint，得到以该点为起始点，剩下的FrenetFramePath。
  - 更新FrenetFramePath
    - 赋值reference line
    - 设置DiscretizedPath

### speed
- SpeedData:
  

  
