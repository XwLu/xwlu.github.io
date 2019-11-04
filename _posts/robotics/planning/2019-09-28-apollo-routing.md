---
layout: post
title: Apollo routing
categories: [Robotics]
description: 源码阅读
keywords: apollo, routing
---

### Routing Map
```
hdmap_version: "03/10/17_22.46.20"
hdmap_district: ""
node {
  lane_id: "1_-1"
  length: 30.347678575236987
  left_out {
    start {
      s: 0
    }
    end {
      s: 30.347678575236987
    }
  }
  cost: 29.203235383821113
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
      s: 0
      start_position {
        x: 586392.84003
        y: 4140673.01232
      }
      length: 30.34767
    }
  }
  is_virtual: false
  road_id: ""
  }
} 
```

### Routing data structure

