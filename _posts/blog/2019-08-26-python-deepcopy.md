---
layout: post
title: Python DeepCpoy
categories: [Python]
description: 总结一下使用python deepcopy的坑
keywords: python, deepcopy
---

### 现象&需求

在调用copy.deepcopy函数时发现，该函数的耗时非常高。虽然python本身不是什么高效率的语言，但是在做算法验证的时候，还是希望他能跟上采集到的传感器数据的播放频率的。

### 原因分析

在deepcopy的源码实现中，他引入了一个memo容器，用来存储已经拷贝的对象。在某些情况下一个对象的成员有可能指向它自己本身，比如双向链表。如果不将拷贝过的对象存着，那程序将陷入死循环。每次拷贝前，程序都要去memo里检查一下是否已经拷贝过，这就是它慢下来的原因。

### 解决方案

大部分时候，我们需要拷贝的对象不存在循环引用，这时我们可以为需要进行拷贝操作的对象实现一个__deepcopy__()函数。

```
class Object(object):
    def __init__(self):
        self.mem_1 = None
        self.mem_2 = None

    def __deepcopy__(self,memodict={}):
        tmp = Object()
        tmp.mem_1 = self.mem_1
        tmp.mem_2 = self.mem_2
        return tmp

if __name__ == "__main__":
    obj_0 = Object()
    obj_1 = obj_0.__deepcopy__()
```
