---
layout: wiki
title: Type
categories: [C++]
description: type for C++
keywords: type, C++
---

# 初始化&赋值
- 功能是将某个值与一个对象关联起来
  - 值：字面值、对象（变量、常量）所表示的值
  - 标识符：变量、常量、引用
  - 初始化基本操作
    - 在内存中开辟空间，保存相应的数值
    - 在编译器中构造符号表，将标识符与相关内存空间关联起来
  - 值和对象都有类型
  - 初始化、赋值都可能涉及到内存转换

# 类型描述
- 类型是一个编译期概念，可执行文件中不存在类型的概念
- C++是强类型语言
- 类型描述了
  - 存储尺寸(sizeof)
  - 取值空间(numric_limit)
  - 对齐信息(align of)
  - 可执行的操作
- 使用int8_t, int16_t, int32_t等去强制确定类型的大小
- 为字面值引入前后缀改变其类型
  - 1.3(double), 1.3f(float)
  - 2(int), 2ULL(unsigned long long)
- 可以自定义字面值后缀
  ```
  int operator "" _ddd(long double x) {  // 注意：这里能支持自定义后缀的数据类型就只有明确的几种，可以去cppreference里搜user_literal查看
    return (int)x * 2;
  }

  int main() {
    int x = 3.14_ddd;  // 6
  }
  ```

# 变量及其类型
- 变量的初始化
  ```
  int x;  // 缺省初始化
  int x(10);  // 直接初始化
  int x{10};  // 直接初始化
  int x = 10;  // 拷贝初始化
  ```
- 为变量赋值时可能发生隐式类型转换
  ```
  unsigned int x = -1;  // x为最大的整数
  ```
- 隐式类型转换不只发生在赋值时
  - if判断
    ```
    if (3) {
      // true
    }
    if (-1) {
      // true
    }
    if (0) {
      // false
    }
    ```
  - 数值比较
    ```
    int x = -1;
    unsigned int y = 3;
    std::cout << (x < y) << "\n";  // 当无符号数与带符号数进行比较时，C++都转化为无符号数进行比较，所以这里返回false
    ```
    - C++20中引入了cmp_xxx操作去处理上面的异常

# 指针
- void*指针
  - 没有记录对象的尺寸信息，可以保存任意地址（某些情况下，我们只需要保存一个指针，而不关注这个指针指向对象的类型）
    ```
    void fun(void* param) {
      std::cout << (param + 1) << "\n";  // 报错，不知道param的尺寸，不知道+1是向后移动多少位
    }
    ```
  - 可以转换为任意类型的指针，也可以由任意类型的指针转换得到
  - 支持判等操作


# 引用
- 是对象的别名，不能绑定字面值
- 构造时绑定某个对象，生命周期之内不能绑定其他对象
- 不存在空引用，但存在非法引用——总的来说比指针安全
  ```
  int& fun() {
    int x;
    return x;
  }

  int main() {
    int& res = fun();  // 编译能通过，但非法
  }
  ```
- 属于编译期概念，底层还是通过指针去实现
- 指针的引用
  - 指针是对象，可以引用
    ```
    int* p = &val;
    int* & ref = p;  // 类型信息从右向左引用
    ```
- 指针对比引用
  - 指针可以为空，引用肯定指向某个内存对象
  - 指针的地址可能非法
  - 引用的本质就是不可变的、合法的、指向某块内存的指针

# 常量引用
```
constexpr T xx;  // xx的数据类型还是const T，只不过constexpr表示它的初始值在编译期就确定了
constexpr const int* ptr = nullptr;  // constexpr修饰ptr，所以ptr的类型是const * const
```

# 类型别名与类型的自动推导
- 两种引入类型别名的方法
  ```
  typedef int MyInt;
  using MyInt = int;  // (since C++11)
  ```
- 使用using引入类型别名更好
  ```
  typedef char MyCharArr[4];  // 写法比较混乱
  using MyCharArr = char[4];
  ```
- 类型别名和指针、引用的关系
  - 应该将指针的类型别名视为一个整体，在此基础上引入的长量表示指针为常量的类型
    ```
    using IntPtr = int*;
    int main() {
      int x = 3;
      const IntPtr ptr = &x;
      const int* ptr = &x;  // 上下两行不等价，上面的const修饰的是ptr，下面的const修饰的是指针指向的内容
    }
    ```
  - 不能通过类型别名构造引用的引用
    ```
    #include <type_traits>
    using RefInt = int&;
    using RefRefInt = RefInt&;
    int main() {
      std::is_same_v(RefInt, RefRefInt);  // true
    }
    ```
- 类型的自动推导
  - 常见形式
    - auto: 最常用的形式，但会产生类型退化
      ```
      int x = 3;
      int& ref = x;
      auto ref2 = ref;  // auto 自动推导出来的类型不是int&，而是int
      ```
    - const auto/constexpr auto: 推导出的是常量、常量表达式类型
    - auto&: 推导出引用类型，避免类型退化
      ```
      const int x = 3;
      auto& y = x;  // y的类型是const int&

      int x1[3] = {1, 2, 3};
      auto x2 = x1;  // x2的类型是int*
      auto& x3 = x1;  // x3的类型是int(&)[3]
      ```
    - decltype(exp): 返回exp表达式的类型（如果表达式是左值，会加引用）
      ```
      int x = 3;
      int& y1 = x;
      auto y2 = y1;  // int
      decltype(y1) y3 = y1;  // int&

      int* ptr = &x;
      decltype(*ptr);  // int&, 因为*ptr是个左值，所以会加上引用
      decltype(3.5 + 15l);  // double，表达式是右值，所以不加引用
      ```
    - decltype(val): 返回val的类型
      ```
      int x = 3;
      decltype(x);  // 按照上面的说法，x是左值，所以这里是int&，但其实不是
      // 原因是，有个约定，如果decltype后面跟的是一个变量名，不加引用
      int* ptr = &x;
      decltype(ptr);  // int*, ptr是个左值，但更是一个变量名，所以不加引用

      const int y1 = 3;
      const int& y2 = y1;
      decltype(y1);  // const int
      decltype(y2);  // const int&
      decltype((y1));  // const int&, (y1)是一个表达式，且是一个左值，所以加上引用
      decltype((y2));  // const int&, (y2)是一个表达式，且是一个左值，所以加上引用，但本身已经有引用，不会变成引用的引用
      ```
    - decltype(auto): since C++14
      ```
      int x = 3;
      int& y1 = x;
      auto y2 = y1;  // int，简洁
      decltype(y1) y3 = y1;  // int&，不会引起退化
      decltype(auto) y3 = y1;  // int&，简洁且不会引起退化
      ```
    - concept auto: since C++20
      ```
      std::integral auto y = 3.5;  // 编译失败，auto自动推导出来的类型(double、float)都不属于integral的范畴
      ```