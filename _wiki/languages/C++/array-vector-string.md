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
- 其他操作
  - 求元素的个数
    ```
    extern int array[];  // 下面的三个方法都无法处理这种imcomplete type
    int a[3];
    sizeof(a);  // 12，int占四个字节，这里的a不会退化为指针，C语言的方法，编译期计算
    std::size(a);  // 3， 推荐使用该方法，C++方法，编译期计算
    std::end(a) - std::begin(a);  // 3，运行期计算
    ```
  - 元素遍历
    ```
    int a[3];
    // 方法1
    size_t i = 0;
    while (i < std::size(a)) {
      a[i];
      ++i;
    }
    // 方法2
    auto ptr =  std::cbegin(a);
    while (ptr != std::cend(a)) {
      ++ptr;
    }
    // 方法3
    for (int x : a) {}
    ```

# C字符串
- 本质上是数组char\[\]
- C语言提供了额外的操作
  ```
  #include <cstring>
  char str[] = "Hello";
  strlen(str);
  // strcmp
  ```

# 多维数组
- 类型推导
  ```
  #include <type_traits>
  int x[3][4];  // x是一个数组，包含了3个 int[4]类型的元素
  std::is_same_v(decltype(x[0], int(&)[4]));  // true，x[0]是个表达式，decltype(表达式)会加上引用
  auto ptr = x;  // 类型退化只发生在第一维，ptr类型是int(*)[4]，只有这样在执行ptr+1的时候才知道要往后移动多少位

  using A = int[4];  // 使用类型别名简化初始化
  A x2[3];  // int x2[3][4]
  A* ptr = x2;
  ```
- 初始化
  ```
  int x[2][3] = {1, 2, 3, 4};  // {123}{400}
  int x[2][3] = \{\{1, 2, 3\}, \{4, 5, 6\}\};  // {123}{456}
  int x[2][3] = \{\{1, 2, 3\}, \{4, 5\}\};  // // {123}{450}
  int x[2][3] = \{\{1, 2\}, \{4, 5\}\};  // // {120}{450}
  int x[][3] = {1, 2, 3, 4, 5};  // x的类型是int[2][3]
  int x[][] = \{\{1, 2, 3\}, \{4, 5, 6\}\};  // 报错，只有第一个[]里面的size可以自动推导，后面的都得显示标明
  ```
- 遍历
  - 二维数组遍历的时候，尽量按行遍历，因为一行的元素往往存在同一块内存，cache命中的概率比较高
  ```
  int x[2][3] = {1, 2, 3, 4};
  for (auto& p : x) {  // 这里的&不能少，如果没有&，p会从int(&)[3]退化为int*，无法进入下面的循环
    for (auto& q : p) {
      // ...
    }
  }
  ```
# Vector
- 是内建数组的代替品
- 与数组比，更注重易用性
  - 可复制
  - 可在运行期改变元素个数
- 初始化
  ```
  std::vector<int> x(3);
  std::vector<int> x(3, 1);  // 1, 1, 1
  std::vector<int> x{3, 1};  // 3, 1
  std::vector<int> x = {1, 2, 3};
  ```
- 索引与遍历
  ```
  std::vector<int> x = {1, 2, 3};
  std::cout << x[20] << std::endl;  // 编译通过，运行可能不会报错
  std::cout << x.at(20) << std::endl;  // 编译通过，运行报错，所以这种写法更好
  ```
- 迭代器
  - 模拟指针的行为
  - 包含多种类别，每种类别支持的操作不同
  - vector对应随机访问迭代器
    - 解引用与下标访问
    - 移动
    - 相减求距离
    - 两个迭代器比较
- 其他
  - 添加元素会导致迭代器失效

# String
- 是内建字符串的代替品
- string其实是std::basic_string\<char\>的类型别名，basic_string是一个类模板
