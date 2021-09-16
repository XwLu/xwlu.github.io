---
layout: wiki
title: Class
categories: [C++]
description: class for C++
keywords: class, C++
---

# 结构体
- 仅有声明的结构体是不完整类型

  ```
  struct Str;

  int main() {
    Str s;  // 编译不通过，只有声明没有定义，不知道Str的内部结构，占多大内存
    Str* s;  // 编译通过，指针的内存大小是固定的，不需要知道Str的内部结构和大小
  }
  ```
- 结构体（以及类）的一处定义原则：翻译单元级别
  - 下面这份代码是可以通过编译并运行的，两个相同的结构体在不同的编译单元
  - source.cpp的内容

    ```
    struct Str{
      int x;
    };
    
    void fun() {
      Str m_str;
    }
    ```
  - main.cpp的内容

    ```
    struct Str {
      int x;
    };

    void fun();

    int main() {
      fun();
      Str str;
    }
    ```

# 数据成员的声明与初始化
- (C++11)数据成员可以使用decltype来声明其类型，但不能使用auto
- 数据成员的声明可以引入const、引用、指针等限定
- 数据成员会在构造对象时定义
- (C++11)支持类内成员初始化
- 结构体支持聚合初始化，但不建议使用

  ```
  struct Str {
    int x;
    int y;
  };

  int main() {
    Str s{3, 4};
    // 上面的代码看起来没啥问题，但是万一哪天，结构体中的成员的顺序被移动了，或者x和y中间插入了一个新的成员，那么这里的聚合初始化都可能引发不想要的错误
  }

  ```
- 为了解决上面的问题，C++20引入了指派初始化

  ```
  Str s{.x=3, .y=4};
  ```

# mutable限定符

  ```
  struct Str {
    mutable int x;
    int y;
  };

  int main() {
    const Str s;
    s.x = 3;  // 编译通过
    s.y = 4;  // 编译不通过
  }

  ```
# 静态数据成员
- 多个对象之前共享的数据成员
- 定义方式的演化
  - C++98：1. 类外定义 2. const静态成员的类内初始化 

    ```
    struct Str {
      static int x;
      int y;
    
      const static array_size = 100;  // 如果没有使用const static，就无法定义Str，因为编译器不知道要分配多大的内存给对象
      int buffer[array_size];  // C++98会把这里的array_size直接替换成100
    };
    
    int Str::x;  // 这里的定义不能少
    
    int main() {
      Str s1;
      Str s2;
      s1.x = 100;
      s2.x = 1;
      std::cout << s1.x << std::endl;  // 1
    
      std::cout << &(s1.array_size) << std::endl;  // 编译不通过，因为C98标准的编译器直接把用到array_size的地方替换为100了，并没有真的分配一块内存来存放array_size
      // 如果一定要给它分配一个地址的话，需要在某个地方加一行定义：int Str::array_size;
    }
    ```
  - C++17：内联静态成员的初始化

    ```
    struct Str {
      inline static int array_size = 100;  // 不需要const了，可修改
      int buffer[array_size];
    };
    
    std::cout << &(s1.array_size) << std::endl;  // 编译通过
    ```
- 静态数据成员可以使用auto推导类型

  ```
  struct Str {
    inline static auto array_size = 100;
  };
  ```
- 静态数据成员的访问
  - str.x
  - str->x
  - Str::x

- 在类的内部声明相同类型的静态数据成员

  ```
  struct Str {
    Str s;  // 编译不通过，因为不知道要给Str分配多大内存，又是鸡蛋问题
    static Str s;  // 编译通过，因为它被所有的Str对象共享，所以它不属于任何一个对象，所以在构建Str对象的时候，不用考虑它所占的内存
  };

  Str Str::s;  // 如果没有这一行，编译能通过，但是link不行，因为s没有被定义，就没有对应的内存 

  int main() {
    Str s1;
    Str s2;
    std::cout << &(s1.s) << std::endl;
  }

  ```
- inline使用的注意点
  - 错误使用方法

    ```
    struct Str {
      inline static int x;  // 编译通过
      inline static Str s;  // 编译不通过，因为使用inline后，这里就不是声明而是定义了，但程序执行到这一行的时候Str还是不完全类型，所以无法完成定义的操作
    };
    
    
    int main() {
      Str s1;
      Str s2;
      std::cout << &(s1.s) << std::endl;
    }
    
    ```
  - 正确使用方法

    ```
    struct Str {
      static Str s;
    };
    
    inline static Str::s;   

    int main() {
      Str s1;
      Str s2;
      std::cout << &(s1.s) << std::endl;
    }
    ```

---

# 成员函数
- 声明与定义
  - 类内定义(隐式内联)

    ```
    // str.h
    struct Str {
      int x;
      void fun() {}
    };

    // main.cc
    #include "str.h"
    
    //main2.cc
    #include "str.h"
    ```
  - 类内声明+类外定义

    ```
    // str.h
    struct Str {
      int x;
      void fun();
    };

    // str.cc
    void Str::fun() {}
    
    // main.cc
    #include "str.h"
    
    // main2.cc
    #include "str.h"
    ```
    - 错误示范

      ```
      // str.h
      struct Str {
        int x;
        void fun();
      };
      void Str::fun() {}  // 报错，重复定义，不能简单将类外定义实现在头文件里，除非改成下面一行
      inline void Str::fun() {}  // 通过，内联函数（也可以直接把fun的定义写在类内，隐式内联）

      // main.cc
      #include "str.h"

      // main2.cc
      #include "str.h"
      ```
  - 类与编译期的两遍处理
    - 我们在定义类的时候，喜欢把成员函数放前面，成员变量放后面
    - 当某些成员函数采用类内定义的时候，它用到的成员函数的定义还在后面
    - 所以C++引入了两遍处理逻辑，看到类内定义的时候，只当作声明，等到把类的声明都过一遍之后再回来处理隐式内联函数的定义
  - 成员函数与尾随返回类型(trail returning type)

    ```
    // str.h
    struct Str {
      using MyRes = int;
      MyRes fun();
      int x;
    };

    // str.cc
    #include "str.h"
    MyRes Str::fun() {  // 错误，MyRes是属于Str类域的，需要改成Str::MyRes
      return x;
    }
    
    auto Str::fun() -> MyRes {  // 合法，因为编译器从前面的Str::已经知道我们在处理Str类内的成员函数了，因此后面的MyRes就会从Str类域里去找定义了
      return x;
    }
    ```
  - 成员函数与this指针
    - 每一个成员函数被调用的时候，编译器都会隐式地传一个this指针进去，用于确定函数里的成员变量是属于哪个对象的。
    - 基于const的成员函数重载
      - this是被const修饰的，所以你不能执行this = nullptr这种，但是你可以操作this指向的内容
      - 如果在成员函数的末尾加上const，传入的参数就变成const Str* const this，此时this和它指向的内容都无法被修改 

        ```
        struct Str {
          // 下面这两个函数能够形成重载，因为传入的this指针类型不一样
          void fun(int x);  // Str* const this
          void fun(int x) const;  // const Str* const this
        };
        ```
  - 成员函数的名称查找与隐藏关系
    - 函数内部（包括形参名称）隐藏函数外部
    - 类内部名称隐藏类外部名称
    - 使用this或域操作符引入依赖型名称查找(::x表示全局变量x)
  - 静态成员函数
    - 用于描述与类紧密相关，但又不需要一个对象实例就可以调用的函数
    - 被所有该类型对象所共享
    - 在调用该函数的时候，不会隐式传入this指针了
    - 因为上一条特性，所以它不能调用一般成员变量，但可以返回静态的数据成员
  - 成员函数基于引用限定符的重载(C++11)

    ```
    class Type {
      void foo() &;
      void foo() &&;
      void foo() const &;
      void foo() const &&;
    };
    
    int main() {
      Type obj;
      obj.foo();  // void foo() &;
      Type().foo();  // void foo() &&;
    }
    ```

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

---

# 构造函数
- 代理构造函数(C++11)
- 初始化列表
  - 提高代码性能
  - 类中的引用成员必须使用初始化列表进行初始化
  - 类成员的初始化顺序与声明的顺序一致，与其在初始化列表中的顺序无关
    ```
    class Str {
    public:
      Str(size_t input):y(input), x(y + 1) {
        std::cout << x << " " << y << std::endl;
      }
    private:
      size_t x;
      size_t y;
    };
    // 打印结果是：4199057 4
    // 因为x和y的初始化顺序是先初始化x，再初始化y，而初始化x时，y还没被初始化，所以y+1的结果是个随机值
    ```
  - 使用初始化列表可以覆盖类内成员初始化的行为
- 缺省构造函数：不需要提供实际参数就可以调用的构造函数
  - 如果类内没有提供任何构造函数，在条件允许的情况下，编译器会合成一个缺省构造函数
    - 如果类内有构造函数，编译器就不会合成缺省构造函数了
    - 如果类内有引用成员变量，编译器也不会合成缺省构造函数
  - 调用缺省构造函数时避免most vexing parse
    ```
    struc Str {
      int x;
    };
    int main() {
      Str m;  // 合法
      Str m();  // 不合法，编译器会把这行当作一个函数声明
      Str m{};  // 合法
      std::cout << m.x << std::endl;
    }
    ```
  - 使用default关键字定义缺省构造函数
    - 当我们在类内定义了一个带参数的构造函数后，编译器不会自动合成缺省构造函数了，这时候我们可以用Str() = default;来自己构造一个
- 单一构造函数
  - 可以是为一种类型转换函数

    ```
    struct Str {
    public:
      Str(int x) : val(x) {}
    private: 
      int val;
    };

    void fun(Str m) {}
    
    int main() {
      Str m(3);  // 合法
      Str m{3};  // 合法
      Str m = 3;  // 合法
      fun(3);  // 合法，发生了隐式类型转换 
    }
    ```
  - explict关键字避免求值过程中的隐式类型转换

    ```
    struct Str {
    public:
      explict Str(int x) : val(x) {}
    private: 
      int val;
    };
    
    void fun(Str m) {}
    
    int main() {
      Str m(3);  // 合法
      Str m{3};  // 合法
      Str m = 3;  // 不合法，explict起作用了
      fun(3);  // 不合法，explict起作用了
    }
    ```
- 拷贝构造函数
  - 会在涉及到拷贝初始化的场景被调用，比如：参数传递。因此需要注意拷贝构造函数的形参类型
    - 拷贝构造函数需要传入引用，最好再加上const
    - 如果不传引用，首先需要将实参拷贝给形参，就需要调用拷贝构造函数，而拷贝构造函数正在被定义中，就无限嵌套了
  - 如果未显式提供，编译器会自动合成一个，合成版本会依次对每个数据成员调用拷贝构造函数，因此拷贝构造函数也可以使用default来构造
- 移动构造函数(C++11)：接受一个当前类右值引用对象的构造函数

  ```
  std::string ori("abc");
  std::string new_str = ori;  // 拷贝构造函数
  std::cout << ori << " " << new_str << std::endl;  // abc abc
  
  std::string new_str2 = std::move(ori);  // 移动构造函数
  std::cout << ori << " " << new_str2 << std::endl;  //  abc
  ```
  - 自定义移动构造函数

    ```
    struc Str {
      // 构造函数
      Str() = default;
      Str(const Str&) = default;
      Str(Str&& x) : val(x.val), a(std::move(x.a)) {};
      
      // void fun() {std::cout << val << " " << a << std::endl;}
      int val = 3;
      std::string a = "abc";
    };

    int main() {
      Str m;
      m.fun();  // 3 abc 
      Str m2 = std::move(m);
      m.fun();  // 3
      m2.fun();  // 3 abc
    }
    ```
  - 缺省移动构造函数
  
    ```
    struct Str2 {
      Str2(const Str2&);
    };
  
    struct Str {
      // 缺省构造
      Str() = default;
      // 拷贝构造
      Str(const Str&) = default;
      // 移动构造
      Str(Str&&) = default;
      // 拷贝赋值
      Str& operator= (const Str& x) {
        std::cout << "copy assignment is called" << std::endl;
        val = x.val;
        a = x.a;
        return *this;
      }
      // 移动赋值
      Str& operator= (Str&& x) {
        std::cout << "move assignment is called" << std::endl;
        val = std::move(x.val);
        a = std::move(x.a);
        return *this;
      }

      int val;
      std::string a;
      Str2 m_str2;
    };

    int main() {
      Str m;  // 编译失败
      // 因为Str2有拷贝构造函数了，编译器不会自动合成缺省构造函数，而Str的default构造函数又需要调用Str2的缺省构造函数，所以就报错了
      // 解决方案：在Str2类内加入"Str2() = default;"

      Str m2 = std::move(m);  //移动构造
      // 这里会依次对Str类的成员变量调用移动构造，由于Str2没有定义移动构造，所以会执行Str2的拷贝构造
      
      Str m3 = m;  // 拷贝构造

      Str m4;
      m4 = m;  // 拷贝赋值
      m4 = std::move(m);  // 移动赋值
    }
    ```
  - 移动构造函数通常声明为不可抛出异常函数：在函数声明的后面加上"noexcept"
  - 注意右值引用对象用作表达式时是左值
    ```
    void fun(Str&& x) {
      std::cout << x.val << std::endl;  // 在这一行里面，x是个左值
    }
    ```
- 拷贝赋值函数(operator=)
  - 代码如上
  - 拷贝赋值不可以使用初始化列表
  - 拷贝赋值函数通常返回该类型的引用
    - 目的是为了支持"m1=m2=m3;"的使用方式
    - 也可以返回void，比如把上面的"return *this"去掉，这样也可以赋值，但是不支持连等操作了
  - 一些情况下编译器会自动合成
- 移动赋值函数
  - 不可以使用初始化列表
  - 通常返回该类型的引用
  - 注意给自身赋值的情况

    ```
    struct Str {
      Str& operator= (Str&& x) {
        if (&x == this) {
          return *this;
        }
        delete ptr;
        ptr = x.ptr;
        x.ptr = nullptr;
      }

      int* ptr;
    };

    int main() {
      Str m1;
      m1 = std::move(m1);  // 如果没有一开始的if判断，这里会把m1的指针给搞成nullptr，显然不是我们想要的
    }
    ```
  - 一些情况下编译器会自动合成 

---

# 析构函数
- 无参数，无返回值，用于释放内存
- 内存回收是在析构函数执行完才进行
- 除非显示声明，否则编译器会自动合成一个，其内部逻辑为平凡的
- 析构函数通常不能抛出异常
  - 因为C++在抛出异常的时候会把当前域内的数据都析构掉，如果析构过程又有新的异常，C++会直接退出，因为C++无法处理多个异常同时抛出的情况
  ```
  ~Str() noexcept = default;
  ```

---

# 相关注意点
- 通常来说，一个类
  - 如果需要定义析构函数，那么也需要定义拷贝构造和拷贝赋值函数
  - 如果需要定义拷贝构造函数，那么也需要定义拷贝赋值函数
  - 如果需要定义拷贝构造（赋值）函数，那么也需要定义移动构造（赋值）函数
- default关键字
  - 只对特殊成员函数有效（比如你只能对构造函数、析构函数等使用default方法）
- delete关键字
  - 对所有函数都有效
    ```
    void fun(int) = delete;
    void fun(double) { std::cout << "double is called"; }

    class Str {
    public:
      Str() = default;
      ~Str() = delete;
    private:
      int* ptr;
    };

    int main() {
      fun(3);  // 程序报错，因为根据名称查找规则，这里会调用void fun(int)，但是该函数是delete修饰的，不能被调用
      Str a;  // 程序报错，因为变量a在main函数执行结束后会被销毁，但析构函数是delete修饰的，不能被调用
      Str* p = new Str();  // 合法，因为new对象不会被自动销毁，所以不会调用析构函数，但是这么写是有内存泄漏的
      // 同时，如果主动调用delete p;，也无法通过编译
    }
    ```
  - 注意，不要为移动构造（赋值）函数引入delete限定符
    - 如果只允许拷贝，那就引入拷贝构造即可
    - 如果不需要拷贝，那就将拷贝构造声明为delete即可
    - 注意delete移动构造（赋值）对C++17的新影响
      ```
      class Str {
      public:
        Str() = default;
        Str(const Str& val) = default;
        Str(Str&& val) = delete;
      };

      voidf fun(Str val) {}

      int main() {
        fun(Str{});  // C++11会报错，因为传入了一个将亡值，会调用移动构造函数
        // C++17不会报错，因为C++17会优化掉移动构造这一步，这就导致同一份代码在不同环境下表现不一样了
      }
      ```
- 特殊成员的合成行为列表（红色的表示支持但可能会废除的行为）
  - ![special-members](https://github.com/XwLu/xwlu.github.io/blob/master/images/wiki/languages/C++/special-members.png?raw=true)
  - 横着看，最左侧一栏表示如果用户声明了xxx，右边的表示系统的行为。
  - 2014年的标准
  
---

# 字面值类
- 可以构造编译器常量的类型
