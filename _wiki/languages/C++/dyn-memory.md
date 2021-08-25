---
layout: wiki
title: Dynamic Memory
categories: [C++]
description: Dynamic memory for C++
keywords: dynamic memory, C++
---

- sizeof 无法获取动态分配内存的大小, 因为sizeof是在编译期就确定的，而动态内存的大小是在运行期决定的

```
std::vector<int> x;
x.push_back(10);
x.push_back(10);
std::cout << sizeof(x) << std::endl;  // 无法获取x的大小
```

# allocator
- 使用allocator来分配内存和释放内存

```
std::allocator<int> al;
int* ptr = al.allocate(3);
al.deallocate(ptr, 3);
```

- 缺陷：只能分配固定类型的内存，比如上面的例子只能分配int类型
- 推荐使用allocator，因为allocator是C++标准，malloc，aligned_alloc是C标准

---

# malloc & free
- 使用malloc和free来管理内存

```
int* p1 = malloc(4 * sizeof(int));
int* p2 = malloc(sizeof(int[4]));  // same space
free(p1);
free(p2);
```

- 优点：只关注分配类型的大小，不受限于类型
- 缺陷：不能分配对齐内存

---

# aligned_alloc
- 可以分配对齐内存

---

# 动态内存与异常安全

```
int* ptr = new int(3);
/*
  异常触发，跳转到异常处理语句，没有执行下面的delete，造成内存泄漏
  建议使用智能指针，避免该问题
 */
delete ptr;
```
