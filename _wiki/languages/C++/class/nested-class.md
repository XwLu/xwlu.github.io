---
layout: wiki
title: Nested Class
categories: [C++]
description: nested class for C++
keywords: nested-class, C++
---

# 嵌套类
- 在类中定义的类
  - 嵌套类有自己的域，与外围类的域形成嵌套关系
    - 嵌套类中的名称查找失败时会在其外围类中继续查找

  ```
  class Out {
    using MyInt = int;
    inline static int val2 = 3;
    int xxx = 100;
  public:
    class In {
    public:
      inline static MyInt val = val2;
      int vvv = xxx;  // 会报错，上面的int xxx不是定义，而是声明，只有在类被实例化的时候才会创建，所以这里无法对齐进行赋值； 如果有static关键字，就是定义了，所以上面一行合法
    };
  };

  int main() {
    Out::In obj;
    Out::In::val;
  }
  ```
  - 嵌套类和外围类单独拥有各自的成员
