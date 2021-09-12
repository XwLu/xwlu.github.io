---
layout: wiki
title: shared ptr
categories: [C++]
description: shared ptr for C++
keywords: shared_ptr, C++
---

# 指定内存回收逻辑
- 为shared_ptr指定自定义的回收逻辑，用于回收复杂的自定义结构
- 设计内存池的时候，自定义Deletor，可以不用真的执行delete操作，而是直接在Deletor函数中将指针交还给内存池

  ```
  void fun(int* ptr) {
    std::cout << "call delete\n";
    delete ptr;
  }

  int main() {
    std::shared_ptr<int>x(new int(3), fun);
    std::unique_ptr<int, decltype(&fun)> x(new int(3), fun);  // 注意，unique_ptr需要传入两个模板参数，这是规定
  }
  ```

---

# make_shared

  ```
  std::shared_ptr<int> x = new int(3);
  auto x = std::make_shared<int>(3);
  ```

- 优先使用make_shared，原因如下：
  - 任何一个智能指针都包涵两块内存，一块是数据，一块是引用计数
  - 使用new方式构造的智能指针，这两块内存可能离的非常远
  - 我们在操作智能指针的时候，经常是访问完引用计数，就要去访问数据，如果离得远，就会造成cache miss
  - make_shared会尽量将两块内存放在一起,cache命中率会增高，从而性能提升

---

# 智能指针数组
- C++17之前需要自定义Deletor函数执行delete []
- 从C++17开始支持

  ```
  std::shared_ptr<int[]> x(new int[5]);
  ```

- 从C++20开始支持make_shared<T[]>

  ```
  auto x = std::make_shared<int[5]>();
  auto x = std::make_shared<int[]>(5); // 两者相等
  ```

---

# shared_ptr初始化陷阱

  ```
  std::shared_ptr<int> x(new int(5));
  std::shared_ptr<int> y(x.get());  // core, x和y互相不知道对方的存在，当x和y分别析构的时候，会delete两次int(5)这块内存
  std::shared_ptr<int> y(x);  // 合法，x和y知道彼此的存在，引用计数会变成2
  ```

---

# 循环引用
- 以下是shared_ptr的循环引用举例

  ```
  struct Str{
    std::shared_ptr<Str> nei;
    ~Str() {
      std::cout << "~Str is called\n";
    }
  };

  int main() {
    std::shared_ptr<Str> x(new Str);
    std::shared_ptr<Str> y(new Str);
    x->nei = y;
    y->nei = x;  // 循环引用出现，x和y无法正常析构
  }
  ```

- 引入weak_ptr解决循环引用问题:

  ```
  struct Str{
    std::weak_ptr<Str> nei;
    ~Str() {
      std::cout << "~Str is called\n";
    }
  };

  int main() {
    std::shared_ptr<Str> x(new Str);
    std::shared_ptr<Str> y(new Str);
    x->nei = y;
    y->nei = x;
  }
  ```

# weak_ptr的lock方法
- weak_ptr的引入也会引发一些问题

  ```
  struct Str{
    std::weak_ptr<Str> nei;
    ~Str() {
      std::cout << "~Str is called\n";
    }
  };

  int main() {
    std::shared_ptr<Str> x(new Str);
    {
      std::shared_ptr<Str> y(new Str);
      x->nei = y;  // 语句块结束后，y被释放，此时的weak_ptr指向的内存不存在了
    }
  }
  ```

- 为了解决上面的问题，在调用weak_ptr的时候需要调用它的lock方法
- lock会检查weak_ptr指向的内存是否还在
- 如果不在了，返回shared_ptr<T>()
- 如果还在，返回shared_ptr<T>(*this)
- 最终的正确写法为

  ```
  struct Str{
    std::weak_ptr<Str> nei;
    ~Str() {
      std::cout << "~Str is called\n";
    }
  };

  int main() {
    std::shared_ptr<Str> x(new Str);
    {
      std::shared_ptr<Str> y(new Str);
      x->nei = y;  // 语句块结束后，y被释放，此时的weak_ptr指向的内存不存在了
    }
    if (auto ptr = x->nei.lock(); ptr) {
      std::cout << "true branch\n";
    } else {
      std::cout << "false branch\n";
    }
  }
  ```

