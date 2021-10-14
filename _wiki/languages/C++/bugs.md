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
  return a <= b;  // 不能写<=，不满足strict weak ordering，会导致数组访问越界，可能导致未定义问题
}

int main() {
  std::vector<int> vec{1, 1, 1};
  std::sort(vec.begin(), vec.end(), cmp);
  return 0;
}
```

