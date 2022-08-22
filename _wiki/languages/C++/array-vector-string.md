---
layout: wiki
title: Array Vector String
categories: [C++]
description: array, vector, string for C++
keywords: array, vector, string, C++
---

# 数组
- 数组初始化方式
  - 缺省初始化
  - 聚合初始化
    ```
    int a[3] = {1, 2};
    int b[3] = {1, 2, 3};
    int c[] = {1, 2, 3};  // c的类型是int[3]
    ```
- 注意
  - 不能使用auto来声明数组类型
    ```
    #include <typeinfo>
    #include <iostream>

    int main() {
      auto b = {1, 2, 3};
      std::cout << typeid(b).name() << std::endl;  // ./exe | c++filt -t 得到std::initializer_list<int>
    }
    ```
  - 数组不能复制
    ```
    int b[] = {1, 2};
    auto a = b;  // a的类型是int*，发生了类型退化
    auto& c = b;  // auto&可以防止类型退化，c的类型是int(&)[3]
    int d[3];
    d = b;  // 报错
    ```
  - 元素个数必须是一个常量表达式（编译期可计算的值），且值必须大于0
    ```
    int a[3];  // a是一个数组，类型是int[3]
    int x;
    std::cin >> x;
    int b[x];  // C语言从C99开始支持variable length array，但C++标准不支持
    // 虽然C++标准不支持，但clang++或g++的部分版本的编译器实现中还是支持该方法，但不建议使用，在代码跨平台移植的时候会出问题
    ```
  - int和int\[1\]是两个不同的类型
  - 字符串数组的特殊性
    ```
    int main() {
      char str[] = "Hello";  // 类型是char[6]，隐式地在字符串最后加一个'\0'
      char str[] = {'H', 'e', 'l', 'l', 'o'};  // 类型是char[5]
    }
    ```
- 数组的复杂声明
  - 指针数组与数组指针
    ```
    int* a[3];  // a是个数组，存了3个int*指针，类型是int*[3]
    int (*a)[3];  // a是个指针，指向一个int[3]类型的数组，类型是int(*)[3]
    ```
  - 声明数组的引用
    ```
    int a[3];
    int (&b)[3] = a;  // b是a的引用
    // auto& b = a;  // 同上
    int x1;
    int& c[1] = {x1};  // C++不支持引用的数组
    ```
- 数组到指针的隐式转换
  - 会丢失一些信息，比如数组的长度
  - 可以通过声明引用来避免隐式类型转换
  - 注意：不要用extern指针来声明数组
    - unknown bounded array声明
      ```
      // source.cc
      int array[5] = {1, 2};
      int array1[5] = {1, 2};
      
      // main.cc
      extern int array[];  // unknown bounded array
      extern int* array1;

      int main() {
        array[0];  // 编译通过，运行正常
        array1[0];  // 编译通过，运行不正常，强行用指针解释数组的内容，会出屎
      }
      ```
  - 获得指向数组开头和结尾的指针
    ```
    // 这里的array不可以是指针
    // extern int array[];  // 不可以是unknown bounded array
    extern int array[4];  // 这个声明可以
    std::begin(array);  // int*
    std::cbegin(array);  // const int*
    std::end(array);  // int*
    std::cend(array);  // const int*
    ```
  - 指针算数
    - 增加、减少、比较
    - 求距离
    - 解引用
    - 指针索引
    ```
    int a[3] = {1, 2, 3};
    auto ptr = a;
    *ptr;
    *a;
    ptr[1];
    ```