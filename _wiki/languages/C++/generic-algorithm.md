---
layout: wiki
title: Generic Algorithm
categories: [C++]
description: generic algorithm for C++
keywords: generic algorithm, C++
---

# 泛型算法简介
- C++标准库中的泛型算法：<algorithm>, <numeric>, <ranges>
- 泛型算法的实现都不复杂，但优化足够好
- 一些泛型算法与方法同名，实现功能类似，此时建议调用方法而非算法
  - std::find VS. std::map::find
  - 方法会根据对应数据结构的特性做优化，而泛型算法为了确保通用性，不会做特定优化

# 泛型算法分类
- 读算法：给定迭代区间，读取其中的元素并进行计算
  - accumulate/find/count
- 写算法：向迭代器中写入元素
  - 单纯写：fill/fill_n
  - 读+写：transform/copy
  - 注意：写算法一定要保证目标区间足够大
- 排序算法：改变输入序列中元素的顺序
  - sort/unique
  - 注意unique的用法，只把原序列中连在一起的相同元素合并为1个
