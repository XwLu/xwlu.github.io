---
layout: wiki
title: Class
categories: [C++]
description: class for C++
keywords: class, C++
---

# 结构体
- 仅有声明的结构体是不完整类型

```
struct Str;

int main() {
  Str s;  // 编译不通过，只有声明没有定义，不知道Str的内部结构，占多大内存
  Str* s;  // 编译通过，指针的内存大小是固定的，不需要知道Str的内部结构和大小
}
```
- 结构体（以及类）的一处定义原则：翻译单元级别
  - 下面这份代码是可以通过编译并运行的，两个相同的结构体在不同的编译单元
  - source.cpp的内容

  ```
  struct Str{
    int x;
  };
  
  void fun() {
    Str m_str;
  }
  ```
  - main.cpp的内容

  ```
  struct Str {
    int x;
  };

  void fun();

  int main() {
    fun();
    Str str;
  }
  ```

# 数据成员的声明与初始化
- (C++11)数据成员可以使用decltype来声明其类型，但不能使用auto
- 数据成员的声明可以引入const、引用、指针等限定
- 数据成员会在构造对象时定义
- (C++11)支持类内成员初始化
- 结构体支持聚合初始化，但不建议使用

```
struct Str {
  int x;
  int y;
};

int main() {
  Str s{3, 4};
  // 上面的代码看起来没啥问题，但是万一哪天，结构体中的成员的顺序被移动了，或者x和y中间插入了一个新的成员，那么这里的聚合初始化都可能引发不想要的错误
}

```
- 为了解决上面的问题，C++20引入了指派初始化

```
Str s{.x=3, .y=4};
```

# mutable限定符

```
struct Str {
  mutable int x;
  int y;
};

int main() {
  const Str s;
  s.x = 3;  // 编译通过
  s.y = 4;  // 编译不通过
}

```
# 静态数据成员
- 多个对象之前共享的数据成员
- 定义方式的演化
  - C++98：1. 类外定义 2. const静态成员的类内初始化 

  ```
  struct Str {
    static int x;
    int y;
  
    const static array_size = 100;  // 如果没有使用const static，就无法定义Str，因为编译器不知道要分配多大的内存给对象
    int buffer[array_size];  // C++98会把这里的array_size直接替换成100
  };
  
  int Str::x;  // 这里的定义不能少
  
  int main() {
    Str s1;
    Str s2;
    s1.x = 100;
    s2.x = 1;
    std::cout << s1.x << std::endl;  // 1
  
    std::cout << &(s1.array_size) << std::endl;  // 编译不通过，因为C98标准的编译器直接把用到array_size的地方替换为100了，并没有真的分配一块内存来存放array_size
    // 如果一定要给它分配一个地址的话，需要在某个地方加一行定义：int Str::array_size;
  }
  ```
  - C++17：内联静态成员的初始化

  ```
  struct Str {
    inline static int array_size = 100;  // 不需要const了，可修改
    int buffer[array_size];
  };
  
  std::cout << &(s1.array_size) << std::endl;  // 编译通过
  ```
- 静态数据成员可以使用auto推导类型

```
struct Str {
  inline static auto array_size = 100;
};
```
- 静态数据成员的访问
  - str.x
  - str->x
  - Str::x

- 在类的内部声明相同类型的静态数据成员

```
struct Str {
  Str s;  // 编译不通过，因为不知道要给Str分配多大内存，又是鸡蛋问题
  static Str s;  // 编译通过，因为它被所有的Str对象共享，所以它不属于任何一个对象，所以在构建Str对象的时候，不用考虑它所占的内存
};

Str Str::s;  // 如果没有这一行，编译能通过，但是link不行，因为s没有被定义，就没有对应的内存 

int main() {
  Str s1;
  Str s2;
  std::cout << &(s1.s) << std::endl;
}

```
- inline使用的注意点
  - 错误使用方法

  ```
  struct Str {
    inline static int x;  // 编译通过
    inline static Str s;  // 编译不通过，因为使用inline后，这里就不是声明而是定义了，但程序执行到这一行的时候Str还是不完全类型，所以无法完成定义的操作
  };
  
  
  int main() {
    Str s1;
    Str s2;
    std::cout << &(s1.s) << std::endl;
  }
  
  ```
  - 正确使用方法

  ```
  struct Str {
    static Str s;
  };
  
  inline static Str::s;   

  int main() {
    Str s1;
    Str s2;
    std::cout << &(s1.s) << std::endl;
  }
  ```
