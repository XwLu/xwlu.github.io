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
- 可以构造编译期常量的类型
- 其数据成员需要是字面值类型(string就不可以，因为string不是字面值常量)
- 提供constexpr/consteval构造函数
  ```
  class Str {
  public:
    constexpr Str(int val) : x(val) {}  // constexpr表示该值既可以在编译期调用也可以在运行期调用
  private:
    int x;
  };  

  int main() {
    constexpr Str a(3);  // Str的构造函数一定要加constexpr修饰，否则报错
  }
  ```
  - 小心使用consteval
    ```
    class Str {
    public:
      consteval Str(int val) : x(val) {}  // consteval表示该值可以在编译期调用
    private:
      int x;
    };
    
    int main() {
      int x;
      Str b(x);  // 在低版本的编译器里可能不会报错，但是高版本的编译器会报错，导致同一份代码的编译结果不一致
    }
    ```
 - 提供constexpr和consteval成员函数（小心使用consteval）
 - 注意：从C++14起，constexpr/consteval成员函数非const成员函数

---

# 成员指针
- 数据成员指针类型示例：int A::\*;
- 成员函数指针类型示例：int (A::\*)(double);
  ```
  class Str {
  public:
    int x;

    void fun() {};
    void fun(double) {};
  };
  int main() {
    int Str::*ptr = &Str::x;  // 数据成员指针
    // 阅读方法：*表明ptr是个指针，能够访问Str::域内的内容，并且这个成员的类型是int

    int Str::*ptr = &(Str::x);  // 报错，加了括号后含义就变了，就不再是取某个域内的地址了，而是取括号里的内容的地址，这就要求括号内的东西是有定义的，但Str::x目前还没有定义，只是声明，所以会报错

    void (Str::* ptr_fun)() = &Str::fun;  // 成员函数指针
    // 阅读方法：*表明ptr是个指针，能够访问Str::域内的内容，()表示这个东西是个函数，函数的输入是void(省略了)，输出也是void

    auto ptr_fun = &Str::fun;  // 报错，因为无法确定auto的内容，上面有两个fun函数可以选择
  }
  ```
- 成员指针对象赋值：auto ptr = &A::x;
  - 注意不能加()，上面有原因
- 成员指针的使用
  ```
  class Str {
  public:
    int x;
  };

  int main() {
    int Str::*ptr = &Str::x;
    *ptr = 1;  // 报错，ptr指向的只是个声明，并没有明确的定义，所以就没有一块固定的内存来解引用

    Str obj;
    obj.*prt = 1;  // 合理
    Str* p_obj = &obj;
    p_obj->*ptr = 3;  // 合理
  }
  ```
- bind交互
  - 使用bind + 成员指针构造可调用对象
    ```
    class Str {
    public:
      int x;
      
      void fun(double) {};
    };
    
    int main() {
      auto ptr = &Str::fun();
      
      Str obj;
      (obj.*ptr)(100.0);
  
      auto x = std::bind(ptr, obj, 100.0);  // 没有obj的话会报错 
      x();  // 调用fun

      auto ptr2 = &Str::x;
      auto x2 = std::bind(ptr2, obj);
      x2();  // 返回obj.x
    }
    ```
  - 注意这种方法也可以基于数据成员指针构造可调用对象(见上面代码中的ptr2)

---

# 运算符重载
- 使用operator关键字引入重载函数
  ```
  struct Str {
    int val = 3;
    
    auto operator () (int y = 3) {  // 只有operator () 可以有缺省参数
      return val + x;
    }
  
    auto operator + (const Str& x) {
      Str res;
      res.val = val + x.val;
      return res;
    }
  };
  
  Str Add(const Str& x, const Str& y) {
    Str z;
    z.val = x.val + y.val;
    return z;
  }
  
  // auto自动推断返回值需要在C++11后面的版本才可以编译通过
  auto operator + (const Str& x, const Str& y) {
    Str res;
    res.val = x.val + y.val;
    return res;
  }
  
  int main() {
    Str x;
    Str y;
    
    auto z = x + y;
    z = Add(x, y);
  
    z = x(4);  // z.val = 3 + 4
    z = x();  // z.val = 3 + 3
  }
  ```
  - 重载不能发明新的运算(比如不能创造一个@运算符)，不能改变运算的优先级与结合性，通常不改变运算含义。
  - 函数参数个数与运算操作数个数相同，至少一个为类类型
  - 除operator()外其他运算符不能有缺省参数
  - 可以选择实现为成员函数与非成员函数
    - 通常来说，实现为成员函数会以\*this作为第一个操作数(注意==与<==>的重载可以不需要\*this作为第一个操作数)
- 根据重载特性，可以将运算符进一步划分
  - 可重载且必须实现为成员函数的运算符(=,[],(),->与转型运算符)
  - 可重载且可以实现为非成员函数的运算符
  - 可重载但不建议重载的运算符(&&,\|\|,逗号运算符)
    - C++17中规定了相应的求值顺序但没有方式实现短路逻辑(短路逻辑即:A&&B,如果A为非，B不用执行)
  - 不可重载的运算符(如 ?: 三元运算符)
- 相对来说比较特殊的运算符重载
  - 对称运算符通常定义为非成员函数以支持首个操作数的类型转换
    ```
    // 错误示范
    struct Str {
      Str(int x) : val(x) {}
      auto operator + (const Str& input) {
        return Str(val + input.val);
      }
      int val;
    };

    int main() {
      Str x(3);
      Str y = x + 4;  // 通过，4会隐式转换为Str(4)
      Str z = 4 + x;  // 不通过，最好不要将称运算符定义为成员函数
    }
    ```
    ```
    // 正确示范
    struct Str {
      Str(int x) : val(x) {}

      Str& operator= (const std::string& input) {
        val = static_cast<int>(input.size());
        return *this;
      }

      // 定义为友元的原因是val是私有成员，而operator +是一个类外的运算符重载函数
      friend auto operator + (const Str& input1, const Str& input2) {
        return Str(input1.val + input2.val);
      }
      
      // 注意这里要返回引用，因为输出流是不支持拷贝的
      friend auto& operator << (std::ostream& ostr, const Str& input) {
        ostr << input.val;
        return ostr;
      }

      int& operator[] (int id) {  // func1
        return val;
      }

      int operator[] (int id) const {  // func2
        return val;
      }

    private:
      int val;
    };

    int main() {
      Str x = 3;
      x = "1234";
      Str y = 4 + x;  // 合法，我们在类外定了operator + (const Str&, const Str&)函数，4会被隐式转换为Str(4)
      std::cout << x << y;  // 合法

      std::cout << x[0];  // 合法
      x[0] = 1;  // 如果func1不是返回的引用，就不合法，因为右值一般不能出现在等号左边

      const Str cx = 3;
      std::cout << cx[0];  // 如果没有定义func2，就不合法，因为func1没用const修饰，可能对类本身进行修改
    }
    ```
  - 移位运算符一定要定义为非成员函数，因为其首个操作数类型需要是流类型(如上面代码所示)
  - 赋值运算符也可以接受一般参数，比如上面代码中传入了一个string。
  - operator []通常返回引用，某些情况下不返回引用，比如上面的func2
  - 自增、自减运算符的前缀、后缀重载法
    ```
    Str& operator++ () {  //如果()里是空的，对应前缀自增x++
      ++val;
      return *this;
    }
    Str operator++ (int) {  // 如果()里有个int变量，对应后缀自增++x，注意这里的()里面的变量没有任何意义，不会参与任何计算或者赋值
      Str tmp(*this);
      ++val;
      return tmp;
    }
    // 从上面的代码可以看出，能用前缀自增就用前缀，更加高效
    ```
  - 使用解引用运算符(*)与成员访问运算符(->)模拟指针行为
    ```
    struct Str {
    public:
      Str(int* p) : ptr(p) {}
      
      int& operator * () {
        return *ptr;
      }

      Str* operator -> () {
        return this;
      }

      int val = 5;

    private:
      int* ptr;
    };
  
    int main() {
      int x = 100;
      Str ptr(&x);
      std::cout << *ptr << std::endl;
      std::cout << ptr->val << std::endl;
      // C++编译的时候遇到->就会去查看有没有operator->重载，如果有重载的话，就把它当作一个类成员函数去调用
      // 所以ptr->val会被翻译为：ptr.operator->()->val，其中ptr.operator->()的返回值就是一个Str*类型的指针
    }
    ```
    - 注意，"."运算符不能重载
    - "->"会递归调用"->"操作
      ```
      struct Str2 {
        Str2* operator -> () {
          return this;
        }

        int val2 = 123;
      };

      struct Str {
        Str2 operator -> () {
          return Str2{};
        }
        
        int val = 5;
      };

      int main() {
        Str ptr = Str();
        std::cout << ptr->val2 << std::endl;
        // 由于Str的operator->()函数返回的是Str2，不是一个指针类型，所以编译期还会继续调用Str2的operator->()函数，直到返回一个指针为止
        // ptr.operator->().operator()->val2
      }
      ```
  - 使用函数调用运算符构造可调用对象(lambda表达式就是通过这个手段实现的)
    ```
    struct Str {
      Str(int p) : val(p) {}

      bool operator () (int input) {
        return val++ < input;
      }

    private:
      int val;
    };

    int main() {
      Str obj(100);
      std::cout << obj(99) << std::endl;  // 0
      std::cout << obj(102) << std::endl;  // 1
      std::cout << obj(102) << std::endl;  // 0, 因为val++
    }
    ```
  - 类型转换运算符
    - 函数声明为operator type() const
      ```
      struct Str {
        Str(int p) : val(p) {}
        
        operator int() const {
          return val;
        }

        friend auto operator + (Str a, Str b) {
          return Str(a.val + b.val);
        }

      private:
        int val;
      };

      int main() {
        Str obj(100);
        static_cast<Str>(100);  // 通过
        static_cast<int>(obj);  // 通过
        int v = obj;  // 通过
        std::cout << v << std::endl;

        obj + 3;  // 不通过，编译器既可以选择把obj转为int再相加，也可以把3转换为Str类型再相加（上面实现了Str的+友元重载）
        // 解决方案就是在Str的构造函数或者型转换运算符重载函数前面加上explicit关键字
        // 如果在构造函数前加explicit，3就不能隐式转换为Str类型了
        // 注意，不能两个函数前同时加上explicit，如果都加了也编译不过，因为没有任何隐式转换发生的话，Str和int无法执行+。

        // 如果在两个函数前同时加上explicit，代码就要这么改
        obj + static_cast<Str>(3);
        static_cast<int>(obj) + 3;
      }
      ```
    - 与单参数构造函数一样，都引入了一种类型转换方式
    - 注意避免引入歧义性与意料之外的行为
      - 通过explicit引入显示类型转换(参考上面的代码)
    - explicit bool的特殊性：用于条件表达式时会进行隐式类型转换
      ```
      struct Str {
        explicit Str(int p) : val(p) {}

        explicit operator bool() const {
          return val == 0;
        }

      private:
        int val;
      };

      int main() {
        Str obj(100);    
        std::cout << obj << std::endl;  // 不通过，因为上面有explicit修饰，不能隐式转换了

        if (obj) {  // 通过，即使定义了explicit，但这里仍然会进行隐式类型转换
          std::cout << 1 << std::endl;
        } else {
          std::cout << 0 << std::endl;
        }

        auto var = obj ? 1 : 0;  // 通过
        std::cout << var << std::endl;
      }
      ```
  - C++20中对==与<==>的重载
    - 在C++20之前，如果我们想重载大小比较，我们需要实现6个重载，==, !=, >=, <=, >, <。 
    - 在C++20之后，只需要重载==, <==>这两个就可以完成对上面6个功能的定义
    - 通过==定义!=
    - 隐式交换操作数
      ```
      #include <compare>
      struct Str {
        Str(int p) : val(p) {}

        friend bool operator == (Str obj, int obj2) {
          return obj.val == obj2;
        }

        auto operator <==> (int x) {
          return val <==> x;
        }

      private:
        int val;
      };

      int main() {
        Str obj(100);
        std::cout << (obj == 100) << std::endl;
        std::cout << (obj != 100) << std::endl;  // C++20之后通过
        std::cout << (100 == obj) << std::endl;  // C++20之后通过，C++20会先尝试找(int, Str)，如果找不到会继续找(Str, int)

        std::cout << (100 >= obj) << std::endl;
        std::cout << (obj >= 100) << std::endl;
      }
      ```
    - 通过<==>定义多种比较逻辑(参考上main的代码)
      - 注意<==>可以返回的类型，strong_ordering, weak_ordering, partial_ordering
---

# 类的继承
