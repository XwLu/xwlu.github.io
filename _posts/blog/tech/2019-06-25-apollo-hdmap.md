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

## 代码框架

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


