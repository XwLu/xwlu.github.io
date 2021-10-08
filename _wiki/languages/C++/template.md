---
layout: wiki
title: Template
categories: [C++]
description: Template for C++
keywords: template, C++
---

# 函数模板
- 使用template关键字引入模板
  - 函数模板的声明与定义
    ```
    // 声明
    template<typename T>
    void fun(T);

    // 定义
    template<typename T>
    void fun(T input){
      std::cout << input << std::endl;
    }
    ```
  - typename关键字可以替换为**class**，含义相同
  - 函数模板中包含了两对参数：函数形参(上面的input)/实参；模版形参(上面的T)/实参
    - **注意**：函数模版不是函数(不能调用)，我们需要在**编译期**给模版形参赋值相应的实参，才能把函数模版实例化成一个函数(可以去cppinsights里面去看看上面这份代码被预处理后的样子)
    - 函数形参是**运行期**赋值的
- 函数模板的显示实例化
  ```
  fun<int>(3);  // 会在终端打印出“3”
  ```
  - 编译期的两阶段处理
    - 模板语法检查
      ```
      // 第一阶段只会检查下面这段代码有没有语法错误
      template<typename T>
      void fun(T input){
        std::cout << input << std::endl;
      }
      ```
    - 模板实例化
      ```
      struct Str {};
      // 第二阶段会实例化对应的函数，这里会实例化出来两个函数，其中第二个函数会报错，因为Str没有重载<<运算符
      fun<int>(3);
      fun<Str>(Str{});
      ```
  - 模板必须在实例化时可见——模板函数只需要满足**翻译单元**的一处定义原则(一般函数需要满足**程序**的一处定义原则)
  - 注意与内联函数的异同
    ```
    // header.h
    template<typename T>
    void fun(T input){
      std::cout << input << std::endl;
    }

    inline void normal_fun() {}

    // main.cc
    #include "header.h"

    // source.cc
    #include "header.h"
    ```
    - 上面的代码如果normal_fun不加inline，编译会报错重复定义，因为normal_fun需要满足程序的一处定义原则，加上inline后只需要满足翻译单元的一处定义原则即可
    - 模板函数本身就只需要满足翻译单元的一处定义原则，所以不需要加inline
    - 当然，模板函数也可以加inline，作用是告诉编译器在实例化函数的时候，顺便替换掉函数的调用，直接把函数逻辑嵌入到实例化的位置
- 函数模板的重载
  ```
  template<typename T>
  void fun(T input){
    std::cout << input << std::endl;
  }

  template<typename T>
  void fun(T* input){
    std::cout << *input << std::endl;
  }

  template<typename T, typename T2>
  void fun(T input, T2 input2){
    std::cout << input << std::endl;
    std::cout << input2 << std::endl;
  }

  int x = 3;
  fun<int>(&x);
  fun<int>(x);
  ```
