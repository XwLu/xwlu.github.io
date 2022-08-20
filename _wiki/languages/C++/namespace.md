---
layout: wiki
title: Namespace 
categories: [C++]
description: namespace for C++
keywords: namespace, C++
---

# 嵌套名字空间
- 名字空间可以嵌套，嵌套名字空间形成嵌套域
- 注意同样的名字空间定义可以出现在程序多处，以向同一个名字空间中增加声明或定义
  ```
  namespace A {
    int x;
  }

  namespace A {
    int y;
  }

  int main() {
    A::x;
    A::y;
  }
  ```
- C++17开始可以简化嵌套名字空间的定义
  ```
  namespace A {
    int x;
  }

  namespace A::B {  // C++17才支持这种写法
    int y;
  }

  int main() {
    A::x;
    A::B::y;
  }
  ```

# 匿名名字空间
- 用于构造仅翻译单元可见的内容
  ```
  // main.cc
  namespace {
    int y;
  }

  int main() {
  }

  // source.cc
  namespace {
    int y;  // 这里的y和mian中的y都是对各自的cc文件可见，不冲突，在内存中是两个不同的地址；如果去掉匿名空间，链接的时候就会报错
  }
  ```
- 想要构造仅翻译单元可见的内容还可以用static代替
  ```
  static int x;  // 这里的xyz都是仅对当前翻译单元可见
  static int y;  // 但这样的写法太啰嗦，每个变量都要写一遍static
  static int z;
  ```
- 匿名名字空间可以作为嵌套空间
  ```
  namespace A {
    namespace {  // 这里加匿名空间意义是让A::x这个对象只在当前翻译单元可见，在其他文件访问不到A::x
      int x;
    }
  }
  ```