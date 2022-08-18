---
layout: wiki
title: Union 
categories: [C++]
description: union for C++
keywords: union, C++
---

# 联合
- 引入union的目的是节省内存
  ```
  struct Str {
    int x;
    int y;
  };

  union Str2 {
    int x;
    int y;
  };

  union Str3 {
    char x;
    int y;
  };

  int main() {
    std::cout << sizeof(Str) << std::endl;  // 8
    std::cout << sizeof(Str2) << std::endl;  // 4
    Str2 obj;
    obj.x = 100;
    std::cout << obj.y << std::endl;  // 100， x和y就是同一片内存
    std::cout << sizeof(Str3) << std::endl;  // 4， 会选择int和char中较大的那个，这样两者都可以表示
  }
  ```
- 通常与枚举一起使用
  - 错误使用方法
    ```
    union Str {
      char x;
      int y;
    };

    int main() {
      Str obj;
      obj.x = 'c';
      std::cout << obj.y << std::endl;  // 99
      // 这里的obj.y会得到多少，是未定义的，因为x只有一个字节，x的内容存在4个字节的Str对象中，具体存在哪个字节里是编译器决定的。不同编译器标准不一样
    }
    ```
  - 正确使用方法
    ```
    struct S {
      enum Type {
        Char,
        Int
      };
      union Str {
        char x;
        int y;
      };
      Type t;
      Str obj;
    };

    int main() {
      S s;
      s.t = S::Char;
      s.obj = 'c';
    }
    ```
- 匿名联合
  ```
  struct S {
    enum Type {
      Char,
      Int
    };
    union {
      char x;
      int y;
    };
    Type t;
  };

  int main() {
    S s;
    s.t = S::Char;
    s.x = 'c';
  }
  ```
- 在联合中包含非内建类型(C++11起)
