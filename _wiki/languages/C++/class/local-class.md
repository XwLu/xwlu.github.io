---
layout: wiki
title: Local Class
categories: [C++]
description: local class for C++
keywords: local-class, C++
---

# 局部类
- 可以在函数中定义
- 可以访问外围函数中定义的类型声明，静态对象与枚举
- 可以定义成员函数，但成员函数的定义必须放在类内
- 不能定义静态数据成员
  ```
  void fun() {
    using MyInt = int;
    int val;
    struct Helper {
      MyInt x;
      int y = val;  // 编译失败
      inline static int val2 = 100;  // 编译失败 

      int inc();
      int acc() {  // 编译成功，函数定义在类内部
        x++;
      }
    };

    int Helper::inc() {  // 编译失败，函数内部不能再定义函数
      returen x++;
    }
    Helper h;
  }

  int main() {
    fun();
  }
  ```
