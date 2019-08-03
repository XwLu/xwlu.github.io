---
layout: post
title: Apollo HDMap
categories: [blog]
description: 阅读apollo hdmap笔记
keywords: apollo hdmap
---

## 前言
前段时间一直在读Apollo地图方面的源码，了解了opendrive和apollo opendrive的地图格式，重构了一份C++的kdtree查找代码并移植到了python下，主要功能是加载地图文件到程序中，并搜索某范围内的道路元素。代码可以在github中找到（C++和python两个版本）。

---

## 地图格式
```
header {
  version: "03/10/17_22.46.20"
  date: "20161124"
  projection {
    proj: "+proj=tmerc +lat_0={37.413082} +lon_0={-122.013332} +k={0.9999999996} +ellps=WGS84 +no_defs"
  }
}
lane {
  id {
    id: "1_-1"
  }
  central_curve {
    segment {
      line_segment {
        point {
          x: 586392.84003
          y: 4140673.01232
        }
        point {
          x: 586392.64623335819
          y: 4140673.061791297
        }
        ......
      }
    }
  }  
  left_boundary{
    curve{
      segment{
        line_segment{
          point{
            x: 
            y:
          }
          point{
            x:
            y:
          }
          ......
        }
      }
    }
    boundary_type{
      s: 0
      types: DOTTED_YELLOW
    }
    length: 156.61603286829296
  }
  right_boundary{
    curve{
      segment{
        line_segment{
          point{
            x:
            y:
          }
          point{
            x:
            y:
          }
          ......
        }
      }
    }
    boundary_type{
      s: 0
      types: CURB
    }
    length: 151.14875772478879
  }
  length: 153.87421245705966
  speed_limit: 4.5
  overlap_id{
    id: "1_-1_and_2"
  }
  type: CITY_DRIVING
  turn: NO_TURN
  left_sample {
    s: 0
    width: 1.7499999999999969
  }
  left_sample {
    s: 0.20001136855364768
    width: 1.7499999999999132
  }
  ......
  right_sample {
    s: 0
    width: 1.7499999999999967
  }
  right_sample {
    s: 0.20001136855364768
    width: 1.7499999999999127
  }
  ......
}

stop_sign {
  id {
    id: "2"
  }
  stop_line {
    segment {
      line_segment {
        point {
          x: 586365.90892295539
          y: 4140785.417460287
        }
        point {
          x: 586367.59595983266
          y: 4140784.9523852509
        }
        point {
          x: 586369.28299449442
          y: 4140784.4873108254
        }
      }
    }
  }
  overlap_id {
    id: "1_-1_and_2"
  }
}

overlap {
  id {
    id: "1_-1_and_2"
  }
  object {
    id {
      id: "1_-1"
    }
    lane_overlap_info {
      start_s: 152.57964692253393
      end_s: 152.57964692253393
    }
  }
  object {
    id {
      id: "2"
    }
  }
}


```
---

## 文件结构和用途
- ### common/math/line_segment
  - 线段对象，由起点和终点构成，定义了该对象的一些距离求解操作
- ### common/math/polygon
  - 多边形对象，由多个corner构成，定义了该对象的一些距离求解操作

- ### common/math/aabox
  - AABox类，用于描述kdtree中每一个对象的位置，包含(min_x, min_y, max_x, max_y, _center, _length, _width)对象。
  - 一般的kdtree都是用点(x,y,z)来表示某个目标(节点)的位置，但是道路这种元素是一个线段，所以不能缩成一个点来看，所以构建了aabox对象用来描述道路、路口的位置。

- ### hdmap_common
  - 将从地图文件中读取道路元素Object(lane, stop_sign, ...。元素的结构在protobuf文件中已经定义好)，解析为ObjectInfo类。
    - protobuf文件中定义的Lane只是车道的结构体数据，而由Lane生成的LaneInfo类才是对车道进行数据操作和访问的对象。其他的同理。
  - 构建模板类ObjectWithAABox
    - 每个ObjectWithAABox类中存储了(aabox，*object, *geo_object, id)一共四个对象。每一个ObjectWithAABox对象就将用于生成一个KDTree Node。
    - aabox: 代表了ObjectWithAABox的位置，在构建kdtree的时候，都是参考的aabox的(min_x, min_y, max_x, max_y)来确定该node在kdtree中的位置。
      - 在apollo高精地图中，所有的道路元素的最小单位只有两种，line_segment和polygon，aabox可以由两个点或多个点来初始化，正好对应line_segment和polygon的初始化。
    - *object: 指向某个ObjectInfo对象的指针，在kdtree中找到该节点之后就找到了对应的ObjectInfo，就可以获取相应的信息。
    - *geo_object: 该节点的几何对象，其实就是LineSegment和Polygon两种。
      - ObjectWithAABox自带DistanceToPoint方法，用于计算自身与某目标点的距离。具体到实现的时候，其实就是调用的geo_object->DistanceToPoint，而该方法在common/math文件夹中的line_segment.cpp和polygon.cpp里已经实现了。
    - id: 记录了该对象所对应的line segment在整条lane中的index，方便后面检索用。

- ### hdmap_impl
  - 地图的从文件读进proto变量
  - proto变量Object加载成ObjectInfo类
  - 构建某个元素的KDTree
    ```
    BuildSegmentKDTree(const Table& table,
                       const AABoxKDTreeParams& params,
                       BoxTable* const box_table,
                       std::unique_ptr<KDTree>* const kdtree)
    ```
    - table是object_info的集合
    - params是事先定好的kdtree参数
    - box_table是从table加载成的ObjectWithAABox的集合
    - kdtree是对应的树的root节点
  - GetObjectById
  - GetObjects
  - GetNearestObject(WithHeading)
  - GetForwardNearestObject
  - GetLocalMap

---

## 代码流程
- ### 从地图文件读取地图内容到protobuf类型的变量
- ### 从protobuf类型的变量生产对应的ObjectInfo。*注意在LaneInfo初始化的时候，会创建一个属于该lane的segments_tree用于快速搜索lane上的segments。*
- ### 创建lane、stop_sign、junction等元素的kdtree，用于搜索附近的道路信息，这些tree是属于HDMap_Impl类的。
- ### 调用HDMap_Impl的接口读取地图信息

- ### overlaps概念
  ```
  LaneInfo.PostProcess->UpdateOverlaps:
  for overlap_id in overlap_ids:
    overlap_ptr = GetOverlapById(overlap_id)
    overlaps.emplace overlaps_back(overlap_ptr)
  
    for object in overlap_ptr->overlap().object():
      object_id = object().id().id()
      if object_id == lane.id().id()
        continue
      if (map_instance.GetSignalById(object_id) != nullptr) {
        signals_.emplace_back(overlap_ptr);
      }
      if (map_instance.GetStopSignById(object_map_id) != nullptr) {
        stop_signs_.emplace_back(overlap_ptr);
      }
      ......
  ```


