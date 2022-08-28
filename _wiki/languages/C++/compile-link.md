---
layout: wiki
title: Compile & Link
categories: [C++]
description: compile & link for C++
keywords: compile, linl, C++
---

# 翻译单元
- 源文件 + 头文件（直接/间接）- 应该忽略的预处理语句
- 源文件所引用的所有头文件都会展开在该源文件中进行编译

---

# 一处定义原则
- 程序级：一般函数
- 翻译单元级：内联函数、类、模板(虽然可以在整个程序中有多处定义，但是需要保证一模一样)
  - 因为编译期是以翻译单元为单位进行编译的，而上面三种类型的代码需要有具体的定义才能正常编译，光有声明是不够的。
  - 这也是为什么Eigen全是头文件，因为它全是模板类，没办法定义在源文件中。[链接](https://blog.csdn.net/l15799033407/article/details/120411311?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-120411311-blog-123913519.topnsimilarv1&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-120411311-blog-123913519.topnsimilarv1&utm_relevant_index=1)

---

# 流程
- 预处理：file.cc -> file.i
  - > gcc -E ./main.cpp -o ./main.i
  - 将源文件转换为翻译单元的过程
  - 防止头文件被循环展开
    - ifdef
    - pragma once（推荐，用ifdef如果后面的文件宏写错了（比如两个不同h文件的宏写成一样了），那就只会include其中一个头文件）。
- 编译：file.i -> file.s
  - > g++ main.i -S -o main.s
  - 将翻译单元转换为相应的汇编语言表示
  - 编译优化
    - https://godbolt.org/z/zh9aqx
  - 增量编译 V.S. 全部编译
    - 有时候只修改了头文件，增量编译不知道需要重新编译，这时候就需要全部编译
- 汇编：file.s -> file.o
  - > g++ main.s -c -o main.o
- 链接：file.o -> file.exe
  - g++ main.o -S -o main
  - 合并多个目标文件，关联声明与定义
  - 种类：内部链接、外部链接、无链接
  - 常见错误：找不到定义
