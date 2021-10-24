---
layout: wiki
title: Fundamentals of meta-programming
categories: [C++]
description: Fundamentals of meta-programming for C++
keywords: meta-programming, fundamental, C++
---

# 元编程的引入
- 从泛型编程到元编程
  - 泛型编程：一套代码处理不同类型
  - 对于一些特殊的类型需要引入额外的处理逻辑——引入**操纵程序的程序**
    - vector<bool>，bool只需要1个bit就可以表示，但是计算机中最小的单位是1个byte，因此，vector对bool类型做了特化
  - 元编程与编译期计算
    - 很多语言都有元编程
    - C++的元编程本质上就是编译期计算
- 第一个元编程示例
  - [Erwin Unruh, 1994](http://www.erwin-unruh.de/primorig.html)
  - 这段代码能够在编译错误中产生质数，也就意味着编译的时候，就有计算被引入其中了，拉开了元编程的序幕
- 使用编译期运算辅助运行期计算
  - 不是简单的将整个计算一分为二
  - 需要详细设计一下，哪些内容可以放到编译期，哪些内容放到运行期
    - 如果某种信息需要在运行期确定，那么通常无法利用编译期计算

---

# 元编程的编写
- 

