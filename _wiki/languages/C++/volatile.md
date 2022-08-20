---
layout: wiki
title: Volatile
categories: [C++]
description: volatile for C++
keywords: volatile, C++
---

# volatile关键字
- 表明一个对象的可能会被当前程序以外的逻辑修改。
- 加了volatile修饰的对象，在读的时候，不能直接从缓存拿，需要去内存拿（因为内存可能已经被其他程序修改了，缓存中的数据存在延迟）；在写的时候，写入缓存后还需要立刻刷新到内存里。
- 注意慎重使用——一些情况下可以用atomic代替
