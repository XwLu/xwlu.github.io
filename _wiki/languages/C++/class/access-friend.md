---
layout: wiki
title: Class Access & Friend
categories: [C++]
description: Access rules & friend for C++ class
keywords: access, friend, class, C++
---

# 访问限定符与友元
- 使用public/private/protected限定类成员的访问权限
  - 类与结构体缺省访问权限的区别：类默认是private，结构体默认是public
- 使用友元打破访问权限限制——关键字friend
  - 声明某个类或者某个函数是当前类的友元——慎用！
  - friend声明放在哪都行(public/private/protected都可以)
  - 在类内首次声明友元类或者友元函数（下面代码中最开头的“声明函数或类”部分可以删掉，可以直接把Str类中的“friend int main();”视作main函数的声明）
    - 注意使用限定名称引入友元并非友元类（友元函数）的声明
      - 比如把Str中的代码改为“friend int ::main();”，编译就不通过了
      - 因为::main()不能被解析为函数的声明，而::main()函数在这之前又没有声明，所以就无法通过编译了
      - 解决方案就是在最前面加上“void main();”，或者把“friend int ::main();”改回“friend int main();”

    ```
    // 声明函数或类
    int main();
    class Str2;

    class Str {
      // 声明main函数和Str2类是当前类的友元
      friend int main();
      friend Str2;  // friend class Str2也可以

      // 默认是private
      inline static int x;
    };

    class Str2 {
      void fun() {
        std::cout << Str::x << std::endl;  // 合法访问
      }
    };

    int main() {
      std::cout << Str::x << std::endl;  // 合法访问，声明了友元
    }
    ```
  - 友元函数的类内类外定义（注意：友元类不可以类内定义）
    - 类外定义
      ```
      // 类外定义
      class Str {
        inline static int x;
        int y;
        friend void fun();
      };
    
      void fun() {
        Str val;
        std::cout << val.y << std::endl;
      }
      ```
    - 类内定义（隐藏友元hidden friend）
      - 错误示例
        ```
        // 类内定义
        class Str {
          inline static int x;
          int y;

          // fun函数不是Str的成员函数，它是Str友元函数，所以fun的作用域是Str外部的全局域
          // 但是fun函数在外部又无法调用，因为fun函数的定义在Str内部，有定义而无声明
          friend void fun() {
            Str val;
            std::cout << val.y << std::endl;
          }
        };
        
        int main() {
          fun();  // 编译失败，常规的名称查找是查找不到fun的
        }
        ```
      - 隐藏友元的正确打开方式
        ```
        // 常规的名称查找找不到fun，Argument-Depende-Lookup(ADL)可以）

        // 类内定义
        class Str {
          inline static int x;
          int y;

          friend void fun2(const Str& val) {
            std::cout << val.y << std::endl;
          }
        };
        
        int main() {
          Str val;
          fun2(val);  // 编译成功
          // 我们在调用fun2的时候传入了val，其类型是Str
          // 所以编译器在执行这条语句的时候除了常规的名称查找，还会进行实参类型的依赖查找
          // 就会扫描Str内部的内容，就会找到fun2
        }
        ```

