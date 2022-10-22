---
layout: wiki
title: Bugs 
categories: [C++]
description: bugs I met
keywords: bugs, C++
---

# sort
```
#include <iostream>
#include <vector>
#include <algorithm>

bool cmp(int a, int b) {
  return a >= b;  // 不满足strict weak ordering，导致快排中的while循环不停++，越界了
}

int main() {
  std::vector<int> vec;
  for (int i = 0; i < 17; ++i) {  // 17就会稳定core，16就不core
                                  // 对于std::sort()，当容器里面元素的个数大于_S_threshold 的枚举常量值时
                                  // 会使用快速排序（stl的这个默认值是16），快速排序中的while循环没有检查越界
    vec.push_back(1);
  }
  std::sort(vec.begin(), vec.end(), cmp);
  return 0;
}
```

# Eigen & auto
- [参考链接](https://blog.csdn.net/weixin_44327262/article/details/127073001)
  ```
  // 用auto接受Eigen的对象时会出错，不要使用
  Eigen::VectorXd dir = (-1.0 * func.grad(x));
  // auto dir = (-1.0 * func.grad(x));
  ```
