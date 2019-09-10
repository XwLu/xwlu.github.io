---
layout: wiki
title: Introduction of python module included in pytorch
categories: [DeepLearing]
description: some word here
keywords: pytorch
---

> ### from __future__ import absolute_import
- 启用绝对引入，指的是优先从系统自带的标准库里寻找您接下来import的库对象。
- 一般写在第一行，否则会从代码的当前目录向下检查有没有目标库，再检索系统自带库。

> ### from __future__ import division
- 导入python未来支持的语言特征division(精确除法)
- 未导入前：
```
  >>> 3/4
  0
```
- 导入后：
```
  >>> 3/4
  0.75
```

> ### from __future__ import print_function
- 即使在python2.X，使用print就得像python3.X那样加括号使用。

> ### from __future__ import unicode_literals
- 即使在python2.X，语法规则也遵循python3.X。

> ### from torch.jit import script, trace
- JIT是一套编译器工具，用于弥合PyTorch研究 与生产之间的差距。它包含一种叫做Torch Script的语言(Python的一个子集)，还有两种方法可以让你的现有代码与JIT兼容。

> ### import torch.nn as nn
- pytorch的神经网络层的对象(多个层也就是模型了，所以模型和层都是nn对象)。

> ### from torch import optim
- pytorch的优化器

> ### import torch.nn.functional as F
- pytorch的函数包

> ### import csv
- 读写csv文件

> ### import unicodedata
- 处理字符的工具，包含查找、转换、检查类型等操作。

> ### import codecs
- codecs专门用作编码转换

> ### import itertools
- Python的内建模块itertools提供了非常有用的用于操作迭代对象的函数
- [参考链接](https://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001415616001996f6b32d80b6454caca3d33c965a07611f000)
