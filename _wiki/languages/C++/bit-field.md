---
layout: wiki
title: Bit Field 
categories: [C++]
description: bit field for C++
keywords: bit field, C++
---

# 位域
- 显示表明对象尺寸（所占位数）
  ```
  struct Str {
    bool b1;
    bool b2;
  };

  struct Str2 {
    bool b1 ： 1;
    bool b2 ： 1;
  };

  int main() {
    sizeof(Str);  // 2, 2个字节，空间浪费
    sizeof(Str2);  // 1, 1个字节（中的两位），剩下的6个位闲置
  }
  ```
- 多个位域对象可能会被打包存取
  - 上面的例子中，程序在处理Str2的时候会多执行1个步骤（取一个字节、位操作得到目标位的值），处理Str的时候只需要取对应的一个字节就行
  - 因此，为了效率用Str，为了省内存用Str2
- 声明了位域的对象不能取地址，因此不能使用指针或非常量引用进行绑定
  - 不能取地址的原因是位域对象不一定在一个字节的开始处
  - 常量引用可以正常工作
    ```
    struct Str {
      bool b1 ： 1;
    };
    Str s;
    const auto& ref = s.b1;  // 这里会把s.b1复制到一个字节大小的临时变量里，然后ref绑定到改临时变量
    ```
  - 这就是为啥遍历vector<bool>的对象只能用“auto&&”或“const auto&”
- 位域指定的大小必须小于其类型的大小
  ```
  char a : 2;  // a的取值是0-3
  char a : 10；  // a的取值还是0-255
  a = 1024;
  std::cout << a;  // 0
  ```