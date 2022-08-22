---
layout: wiki
title: Function
categories: [C++]
description: function for C++
keywords: function, C++
---

# 函数基础
- 函数声明可以出现多次，但函数定义通常只能出现一次（inline函数可以多次）
- 栈帧结构(stack frame)
  - 每次调用一个函数就会在栈上开辟一个frame
  - frame包含了如下信息
    - local variables
    - 返回地址
    - 实参args1, args2, ...
  - 当前frame执行完后返回到前一个frame的地址，遵循stack先进后出的逻辑
- 拷贝过程的（强制）省略
  - 返回值优化
  - C++17强制省略拷贝临时对象
- 函数的外部链接
  - 编译器需要为C++中的所有函数，在符号表中生成唯一的标识符，来区分不同的函数。而对于同名不同参的函数，编译器在进行name mangling操作时，会通过函数名和其参数类型生成唯一标识符，来支持函数重载。
    - nm exetable_file
    - nm exetable_file | c++filt -t: demangling， 得到函数的原始命名
  - demo
    ```
    // func.h
    int Add(int x, int y);

    // func.cc
    int Add(int x, int y) {
      return x + y;
    }
    ```
    - 对上面的代码进行编译，mangling后，Add函数的标识符为_Z3addii，前缀不讨论，后缀的ii表示函数的形参类型是两个int型
    - C++之所以能实现重载，就是因为mangling之后的函数标识符在函数名的基础上追加了形参类型的后缀，这样相同名字的函数只要形参类型不同，编译器依然可以区分开
    - C语言没有这样的后缀，所以C不支持函数重载
    - 如果我们想在一个C文件里调用上面的C++代码，是无法链接到Add函数的，因为C语言想找的函数标识符就是add，没有后缀，与_Z3addii不同
    - 可以通过extern \"C\"去支持C文件的调用
      ```
      // func.h
      extern "C" int Add(int x, int y);
      int Add(int x);

      // func.cc
      extern "C"
      int Add(int x, int y) {  // 标识符为add
        return x + y;
      }

      int Add(int x) {  // 标识符为addi
        return x;
      }
      ```
      - 这样处理后，mangling得到的标识符就变成add了，外部的C文件可以找到该函数进行调用，但同样就不支持重载了
      - 上面的代码两个Add函数都可以被正常调用，因为第二个函数没有extern \"C\"修饰，还是用的C++的nm方法，两者的标识符不一样

# 函数重载
- 相同的函数名，不同的参数列表
  - 不能基于不同的返回值进行重载
  - 函数重载与name mangling
- 名称查找
  - 限定查找(qualified lookup)与非限定查找(unqualified lookup)
    ```
    void fun() {}

    namespace MyNS {
      void fun() {}
    }

    int main() {
      ::fun();  // 限定查找，有明确的域名global
      MyNS::fun();  // 限定查找，有明确的域名MyNS
      fun();  // 非限定查找，函数前没有指定明确的域
    }
    ```
  - 非限定查找会进行域的逐级查找——名称隐藏(hiding)
    ```
    void fun() {}
    void fun1() {}

    namespace MyNS {
      void fun() {}
      void g() {
        fun();  // 会调用MyNS中的fun
      }

      int fun1 = 3;
      void g2() {
        fun1();  // 会报错，因为MyNS::fun1隐藏掉了global::fun1，而MyNS::fun1是个变量，无法执行函数调用
      }
    }

    int main() {
      MyNS::g();
    }
    ```
  - 查找通常只会在已声明的名称集合中进行
    ```
    void fun() {}

    namespace MyNS {
      void g() {
        fun();  // 会调用global中的fun
      }
      void fun() {}
    }

    int main() {
      MyNS::g();
    }
    ```
  - 实参依赖查找(Argument Dependent Lookup: ADL)
    - 只对自定义类型生效
      ```
      struct Str2 {};

      namespace MyNS {
        struct Str {};
        void g(Str x) {}
        void g(Str2 x) {}
      }

      int main() {
        Str2 obj2;
        g(obj2);  // 编译不通过，找不到g
        MyNS::Str obj;
        g(obj);  // 非限定性查找一般来说不会去考虑名字空间内部的东西，但由于这个函数的形参的类型是定义于MyNS内部的一个结构体，编译器就会把对应名字空间内的对象纳入到考虑范围之内，这里的调用就是合法的
      }
      ```
- 重载决议
  - 当有多个函数都符合名称查找时，需要有一系列的规则去决定到底用哪个函数
  - 过滤不能被调用的版本
    - 参数个数不对
    - 无法将实参转换为形参
    - 实参不满足形参的限制条件（一般是用到模板时会遇到，比如C++20 concept限制）
  - 在剩余版本中查找与调用表达式最匹配的版本，匹配级别越低越好（有特殊规则）
    - 级别1：完美匹配 或 平凡转换（比如加一个const）
    - 级别2：promotion或promotion+平凡转换
    - 级别3：标准转换或标准转换+平凡转换
    - 级别4（非C++标准引入）：自定义转换或自定义转换+平凡转换或自定义转换+标准转换
    - 级别5（非C++标准引入）：调用形参为省略号的版本
      ```
      void fun(...) {}  // 可以传入任意参数
      ```
    - 函数包含多个形参时，所选函数的所有形参的匹配级别都要优于或等于其他函数
    - 特殊规则
      ```
      void fun(int& x) {}
      void fun(const int& x) {}

      int main() {
        int x;
        fun(x);  // 上面两个函数都属于级别1的匹配，但编译通过，没有发生冲突
        // 规则是：x为左值，int&优于const int&
      }
      ```

# 内联函数
- 满足翻译单元的一处原则（为了在编译期展开），普通函数需要满足程序的一处原则，所以inline函数可以定义在header中

# constexpr/consteval函数
- 满足翻译单元的一处原则（为了在编译期就可以计算）
- constexpr函数可以在编译期执行也可以在运行期执行（since C++11）
  ```
  constexpr int fun(int x) {
    return x + 1;
  }

  int fun2(int x) {
    return x + 1;
  }

  int main() {
    int y;
    cin >> y;
    constexpr int x = fun(y);  // 运行期执行
    constexpr int x2 = fun(3);  // 编译期执行
    constexpr int x3 = fun2(3);  // 编译失败，因为fun不是常量函数，无法赋值给常量
    return 0;
  }
  ```
- consteval函数只能在编译期执行（since C++20）
  ```
  consteval int fun(int x) {
    return x + 1;
  }

  int main() {
    int y;
    cin >> y;
    constexpr int x = fun(y);  // 编译失败，无法在编译期执行
    constexpr int x2 = fun(3);  // 编译期执行
    return 0;
  }
  ```
# 函数指针
- 函数类型
  ```
  using K = int(int);  // K就是一个函数类型的别名
  K fun;  // 函数声明，等同于int fun(int);
  std::function<void(int)> f;  // 这里的模板参数就是函数类型
  ```
- 函数指针类型
  ```
  int inc(int x) {
    return x + 1;
  }

  int dec(int x) {
    return x - 1;
  }

  using K = int(int);

  void Demo(K in) {}
  void Demo1(K* in) {}

  int main() {
    K* fun = &inc;
    (*fun)(100);  // 101

    auto fun2 = dec;  // 等同于：using F = int(*)(int); F fun2 = dec;
    
    // 下面四种写法效果一样
    Demo(inc);
    Demo(&inc);
    Demo1(inc);
    Demo1(&inc);
  }
  ```
- 函数指针重载
  ```
  void fun(int) {}

  void fun(int, int) {}

  int main() {
    auto f = fun;  // 报错，编译器不知道指向上面哪个fun函数
    void(*)(int) f = fun;  // 通过，类型明确
  }
  ```
- 将函数指针作为函数参数
- 将函数指针作为函数的返回值
  ```
  int inc(int x) {
    return x + 1;
  }

  int dec(int x) {
    return x - 1;
  }

  auto fun(bool input) {
    if (input) {
      return inc;
    } else {
      return dec;
    }
  }

  int main() {
    (*fun(true))(100);  // 101
  }
  ```
- 注意most vexing parse问题
  - 该问题促使C++11引入T xx{};的初始化方式
  - 使用{}而非()去初始化一个变量可以避免该问题的发生