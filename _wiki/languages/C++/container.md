---
layout: wiki
title: Container 
categories: [C++]
description: Container for C++
keywords: container, C++
---

## 容器
### 以下是一些选择容器的基本原则：
- #### 除法你有很好的理由选择其他容器，否则应该使用vector；
- #### 如果你的程序有很多小的元素，且空间的额外开销很重要，则不要使用list或forward_list；
- #### 如果程序要求随机访问元素，应使用vector或deque；
- #### 如果程序要求在容器的中间插入或删除元素，应使用list或forward_list；

### 容器介绍
- list:
  - 头尾都可以操作
  - 插入删除很快,随机访问很慢
- deque:
  - 双端队列，可以高效的在头尾两端插入和删除元素,
  - 双端队列不保证内部的元素是按连续的存储空间存储的，因此，不允许对指针直接做偏移操作来直接访问元素
- set:
  - set的特性是，所有元素都会根据元素的键值自动排序，set的元素不像map那样可以同时拥有实值(value)和键值(key),set元素的键值就是实值，实值就是键值。
  - set不允许两个元素有相同的键值。
- map:
  - 两种赋值方式，其中数组赋值方式遇到key值相同的情况会直接覆盖，insert方式会丢弃之前的数据。
- unordered_map:
  - 关联性：元素根据键来引用，而不是根据索引来引用。
  - 无序性：元素不会根据其键值或映射值按任何特定顺序排序，而是根据其哈希值组织到桶中，以允许通过键值直接快速访问各个元素（常量的平均时间复杂度）。
  - 唯一性：std::unorederd_map中的元素的键是唯一的。

### 容器操作
#### vector
- erase():删除该成员的同时返回指向下一个成员的指针;
```
    vector<int> A;
    vector<int>::iterator it=A.begin()    
    while(it!=A.end()){
        if(*it == 0){
            it = A.erase(it)
            continue;
        }
    }
```