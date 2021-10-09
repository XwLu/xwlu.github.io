---
layout: wiki
title: Expression
categories: [C++]
description: Expression for C++
keywords: expression, C++
---

# 表达式基础
- 操作符
  - [操作符优先级](https://zh.cppreference.com/w/cpp/language/operator_precedence)
  - 操作符重载：不改变接收操作数的个数、优先级与结合性
- 操作数求值顺序的不确定性
  ```
  void fun(int p1, int p2) {
    std::cout << p1 << " " << p2 << std::endl;
  }
  
  int main() {
    int x = 0;
    fun(x = x + 1, x = x + 1);  // 前面的x = x + 1和后面的x = x + 1谁先执行是不确定的
    /*第一种可能的执行顺序
     * x = x + 1
     * x = x + 1
     * p1 = x
     * p2 = x
     * 打印结果 2 2
     */

    /*第二种可能的执行顺序
     * x = x + 1
     * p1 = x
     * x = x + 1
     * p2 = x
     * 打印结果 1 2
     */
  }
  ```
  - 乱序执行的目的是为了效率
    ```
    int a = 1;
    int b = 2;
    a = 3;
    b = 4;
    // 上面这段代码，编译器在编译的时候可能会把b = 4;放到a = 3;前面一行去执行，这样的话cache命中率更高，效率更高
    ```
- 左值&右值
  - 传统的左值与右值划分
    - 来源于C语言：左值可以放在等号左边；右值只能放在等号右边。
  - 所有的划分都是针对表达式的，不是针对对象或者数值
    - [cppreference链接](https://zh.cppreference.com/w/cpp/language/value_category)
    - ![lrvalue](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/languages/C++/lrvalue.png?raw=true)
    - 泛左值(glvalue): 标识一个对象、位域或函数
    - 纯右值(prvalue): 用于初始化对象或作为操作数
      ```
      int x = 3;  // 这是个初始化操作，不是个表达式，这里的的3是个纯右值（用于初始化对象）
      x = 4;  // 这是个表达式，这里的x是个泛左值，3是个纯右值
      ```
    - 将亡值(xvalue): 标识其资源可以被重新使用
      ```
      void fun(std::vector<int>&& input) {}

      int main() {
        std::vector<int> x;
        fun(std::move(x));  // move操作把x转换为一个将亡值
      }
      ```
  - 在C++中，左值也不一定能放在等号左边；右值也可能放在等号左边
    ```
    const int x = 3;  // x是个泛左值，因为它标识了一个对象（一块内存）；x不是将亡值；所以x是个左值（根据上面的图推断）
    x = 4;  // 虽然x是左值，但是它不能放在等号左边

    struct Str {};
    Str() = str();  // 编译可以通过，所以右值可以放在等号左边
    ```
  - 左值与右值的转换
    - 左值转换为右值
      ```
      int x = 3;
      int y = x;  // 这里y需要的是右值，所以C++会把x转换为右值用于y的初始化
      ```
    - 临时具体化
      ```
      struct Str {
        int x;
      };

      int main() {
        Str().x;  // 这里我们需要把Str()从一个纯右值转换为将亡值才能拿到x
      }
      ```
  - 再讨论decltype
    - 如果表达式的值类别为亡值，decltype产生T&&
    - 如果表达式的值类别为左值，decltype产生T&
    - 如果表达式的值类别为纯右值，decltype产生T
    ```
    decltype(3) x;  // int x;
    
    int x;
    decltype((x)) y = x;  // int& y = x;
    ```
