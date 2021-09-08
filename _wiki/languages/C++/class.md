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

---

# 成员函数
- 声明与定义
  - 类内定义(隐式内联)

  ```
  // str.h
  struct Str {
    int x;
    void fun() {}
  };

  // main.cc
  #include "str.h"
  
  //main2.cc
  #include "str.h"
  ```
  - 类内声明+类外定义

  ```
  // str.h
  struct Str {
    int x;
    void fun();
  };

  // str.cc
  void Str::fun() {}
  
  // main.cc
  #include "str.h"
  
  // main2.cc
  #include "str.h"
  ```
    - 错误示范

    ```
    // str.h
    struct Str {
      int x;
      void fun();
    };
    void Str::fun() {}  // 报错，重复定义，不能简单将类外定义实现在头文件里，除非改成下面一行
    inline void Str::fun() {}  // 通过，内联函数（也可以直接把fun的定义写在类内，隐式内联）

    // main.cc
    #include "str.h"

    // main2.cc
    #include "str.h"
    ```
  - 类与编译期的两遍处理
    - 我们在定义类的时候，喜欢把成员函数放前面，成员变量放后面
    - 当某些成员函数采用类内定义的时候，它用到的成员函数的定义还在后面
    - 所有C++引入了两遍处理逻辑，看到类内定义的时候，只当作声明，等到把类的声明都过一遍之后再回来处理隐式内联函数的定义
  - 成员函数与尾随返回类型(trail returning type)

  ```
  // str.h
  struct Str {
    using MyRes = int;
    MyRes fun();
    int x;
  };

  // str.cc
  #include "str.h"
  MyRes Str::fun() {  // 错误，MyRes是属于Str类域的，需要改成Str::MyRes
    return x;
  }
  
  auto Str::fun() -> MyRes {  // 合法，因为编译器从前面的Str::已经知道我们在处理Str类内的成员函数了，因此后面的MyRes就会从Str类域里去找定义了
    return x;
  }
  ```
  - 成员函数与this指针
    - 每一个成员函数被调用的时候，编译器都会隐式地传一个this指针进去，用于确定函数里的成员变量是属于哪个对象的。
    - 基于const的成员函数重载
      - this是被const修饰的，所以你不能执行this = nullptr这种，但是你可以操作this指向的内容
      - 如果在成员函数的末尾加上const，传入的参数就变成const Str* const this，此时this和它指向的内容都无法被修改 

      ```
      struct Str {
        // 下面这两个函数能够形成重载，因为传入的this指针类型不一样
        void fun(int x);  // Str* const this
        void fun(int x) const;  // const Str* const this
      };
      ```
  - 成员函数的名称查找与隐藏关系
    - 函数内部（包括形参名称）隐藏函数外部
    - 类内部名称隐藏类外部名称
    - 使用this或域操作符引入依赖型名称查找(::x表示全局变量x)
  - 静态成员函数
    - 用于描述与类紧密相关，但又不需要一个对象实例就可以调用的函数
    - 被所有该类型对象所共享
    - 在调用该函数的时候，不会隐式传入this指针了
    - 因为上一条特性，所以它不能调用一般成员变量，但可以返回静态的数据成员
  - 成员函数基于引用限定符的重载(C++11)
  ```
  class Type {
    void foo() &;
    void foo() &&;
    void foo() const &;
    void foo() const &&;
  };
  
int main() {
  Type obj;
  obj.foo();  // void foo() &;
  Type().foo();  // void foo() &&;
}
  ```
