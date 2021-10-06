---
layout: wiki
title: Constexpr of Class
categories: [C++]
description: constexpr for C++
keywords: constexpr, C++
---

# 字面值类
- 可以构造编译期常量的类型
- 其数据成员需要是字面值类型(string就不可以，因为string不是字面值常量)
- 提供constexpr/consteval构造函数
  ```
  class Str {
  public:
    constexpr Str(int val) : x(val) {}  // constexpr表示该值既可以在编译期调用也可以在运行期调用
  private:
    int x;
  };  

  int main() {
    constexpr Str a(3);  // Str的构造函数一定要加constexpr修饰，否则报错
  }
  ```
  - 小心使用consteval
    ```
    class Str {
    public:
      consteval Str(int val) : x(val) {}  // consteval表示该值可以在编译期调用
    private:
      int x;
    };
    
    int main() {
      int x;
      Str b(x);  // 在低版本的编译器里可能不会报错，但是高版本的编译器会报错，导致同一份代码的编译结果不一致
    }
    ```
 - 提供constexpr和consteval成员函数（小心使用consteval）
 - 注意：从C++14起，constexpr/consteval成员函数非const成员函数
