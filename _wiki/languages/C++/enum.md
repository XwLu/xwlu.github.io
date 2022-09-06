---
layout: wiki
title: Enum 
categories: [C++]
description: enum for C++
keywords: enum, C++
---

# 枚举
- 一种取值受限的特殊类型
  - 无作用域枚举
    ```
    enum Color {  
      Red,  // Red的作用域不在Color内，而是整个文件，因此main函数中可以直接使用“Red”
      Yellow
    };

    enum Color2 {  
      Red,  // Red的作用域不在Color内，而是整个文件，因此main函数中可以直接使用“Red”
      Yellow
    };

    namespace ABC {
      enum Color {
        Red,  // Red的作用域不在Color内，而是ABC域内
        Yellow
      };
    }

    class DEF {
    public:
      enum Color {
        Red,  // Red的作用域不在Color内，而是DEF类内
        Yellow
      };
    };
    int main() {
      Color x = Red;
      Color2 x2 = Red;  // 这里的Red和上面的Red产生冲突了，这就是无作用域的坏处
      ABC::Color y = ABC::Red;
      DEF::Color z = DEF::Red;
    }
    ```
  - 有作用域枚举（C++11起）
    ```
    enum class Color {  
      Red,  // Red的作用域在Color内
      Yellow
    };

    enum class Color2 {  
      Red,  // Red的作用域在Color2内
      Yellow
    };

    int main() {
      Color x = Color::Red;
      Color x2 = Color2::Red;  // 无冲突了
    }
    ```
  - 枚举项缺省使用0初始化，依次递增，可以使用常量表达式来修改缺省值
    ```
    enum class Color {  
      Red,
      Yellow = 100,
      Green
    };

    class A {
    public:
      enum {x = 3};  // 早期的C++代码通过这种方式在类的内部定义一个编译期常量
      // constexpr static int x = 3;  // 这种方式更好，但早期C++不包含constexpr，所以用上面的方法代替
    };

    int main() {
      std::cout << Color::Green << std::endl;  // 101
    }
    ```
  - 可以为枚举指定底层类型，表明了枚举项的尺寸
    ```
    enum Color {  
      Red,
      Yellow = 100,
      Green
    };

    enum Color2 : char {  
      Red,
      Yellow = 100,
      Green
    };

    int main() {
      std::cout << sizeof(Color) << std::endl;  // 4(int)
      std::cout << sizeof(Color2) << std::endl;  // 1
    }
    ```
  - 无作用域的枚举项可以隐式转换为整数，也可以用static_cast互相转换；有作用域的枚举项不可以
    ```
    enum Color {  
      Red,
      Yellow = 100,
      Green
    };

    void fun(Color x) {

    }

    enum class Color2 {  
      Red,
      Yellow = 100,
      Green
    };

    int main() {
      fun(100);  // 编译失败
      fun(static_cast<Color>(100));  // 编译成功

      std::cout << Red << std::endl;  // 0
      std::cout << Color2::Red << std::endl;  // 编译失败
      std::cout << static_cast<int>(Color2::Red) << std::endl;  // 编译成功
    }
    ```
  - 注意区分枚举的声明和定义
    ```
    enum Color;
    enum Color2 : int;
    enum class Color3;

    int main() {
      Color x;  // 即使在别的文件中有定义，还是失败；因为在这个翻译单元中，编译器不知道Color的具体大小是int还是char还是其他大小，无法分配内存
      Color2 x;  // 可以编译，Color2的大小确定
      Color3 x;  // 可以编译，有作用域的枚举类型默认是int型
    }
    ```
