---
layout: wiki
title: Bind & Lambda 
categories: [C++]
description: Lambda for C++
keywords: bind, lambda, C++
---

[优秀教程](https://www.jianshu.com/p/d686ad9de817)

### 背景
- 很多算法允许通过可调用对象自定义计算逻辑的细节
  - transform / copy_if / sort
- 可调用对象
  - 函数指针：概念直观，但是定义位置受限

  ```
  bool BiggerThan(const int val) {
    return val > 3;
  }
  int main() {
    std::vector<int> x{0, 1, 2, 3, 4};
    std::vector<int> y;
    // 不能把BiggerThan的定义写在这里，因为C++不支持在函数中定义函数
    std::copy_if(x.begin(), x.end(), std::back_inserter(y), BiggerThan);  // y = {4};
  }
  ```
  - 类：功能强大，但是书写麻烦
  - bind：基于已有的逻辑来灵活适配，但描述复杂逻辑时愈发可能会比较复杂难懂
  - lambda表达式：小巧灵活，功能强大

### Bind
- bind：通过绑定的方式修改可调用对象的调用方式

