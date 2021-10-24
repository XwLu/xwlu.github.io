---
layout: wiki
title: Other knowledges of template
categories: [C++]
description: other knowledges of template for C++
keywords: template, C++
---

# 数值模版参数 & 模版模版参数
- 模版接收（编译期常量）数值作为模版参数
  - template <int a>
    ```
    template <int a>
    int fun(int x) {
      return x + a;
    }

    template<3>(5);  // 8
    ```
  - template <T, T value>
    ```
    template <T, T a>
    int fun(int x) {
      return x + a;
    }

    template<int, 3>(5);  // 8
    template<bool, 3>(5);  // 编译失败
    ```
  - template <auto value>，C++17开始
    ```
    template <auto a>
    void fun() {}

    fun<3>();  // 通过
    fun<true>();  // 通过
    ```
  - 接收字面值对象和浮点值作为模板参数，C++20开始
    - clang 20目前不支持用浮点数作为模版参数
- 接收模版作为模版参数
  ```
  template <typename T>
  class C{};
  
  template<template <typename T> class T2>  // 由于这里的T不重要，可以删掉
                                            // template<template <typename> class T2>
                                            // C++17开始，这里的class可以换成typename
                                            // template<template <typename> typename T2>
  void fun() {
    T2<int> tmp;
  }
  
  int main() {
    fun<C>();
  }
  ```
- C++17开始，模版的模版实参会考虑缺省实参(clang可能不行)
  - 错误例子
    ```
    template <typename T1, typename T2>
    class C{};
    
    template <template <typename> typename T>
    void fun() {}
    
    int main() {
      fun<C>();  // 报错，C有两个模版形参，而fun的模板参数的模板形参只有1个
    }
    ```
  - 修正方法1
    ```
    template <typename T1, typename T2>
    class C{};
    
    template <template <typename, typename> typename T>
    void fun() {}
    
    int main() {
      fun<C>();  // 通过，C有两个模版形参，fun的模板参数的模板形参也是2个
    }
    ```
  - 修正方法2
    ```
    template <typename T1, typename T2 = int>
    class C{};
    
    template <template <typename> typename T>
    void fun() {}
    
    int main() {
      fun<C>();  // 通过，C有两个模版形参，fun的模板参数的模板形参只有1个，但是C的第二个模版形参是缺省值
    }
    ```

---

# 别名模版 & 变长模版
- 可以使用using引入别名模版
  - 为模版本身引入别名
    ```
    // demo 1
    template <typename T>
    using AddPointer = T*;

    int main() {
      AddPointer<int> x;  // int* x
    }

    // demo 2
    template <typename T>
    struct Alloc{};
    
    template<typename T>
    using Vec = vector<T, Alloc<T>>;
    Vec<int> v;
    ```
  - 为类模版成员引入别名
    ```
    template <typename T>
    struct B {
      using TP = T*;
    };

    template <typename T>
    using AddPointer = typename B<T>::TP;

    int main() {
      AddPointer<int> x;  // int* x
    }
    ```
  - 别名模版不支持特化
    ```
    // 当T是int时返回T&，其他时候返回T*，但是下面这种写法不行
    template <typename T>
    using MyPointer = T*;

    template <>
    using MyPointer<int> = int&;
    ```
    - 可以基于类模版的特化引入别名，以实现类似于特化的功能
      ```
      template <typename T>
      class B {
        using type = T*;
      };

      template <>
      struct B<int> {
        using type = int&;
      };

      template <typename T>
      using MyPointer = typename B<T>::type;

      int main() {
        MyPointer<float> x;  // float* x;
      }
      ```
    - 注意与实参推导的关系(有点复杂，可以复习C++课程视频)
- [变长模版(Variadic Template)](https://zh.cppreference.com/w/cpp/language/parameter_pack)
  - 模版形参包
    - 类型 ... Args
      ```
      template <int ... a>
      void fun() {}

      int main() {
        fun<1, 2, 3>();  // 可以输入多个int型参数
      }
      ```
    - typename | class ... Args
      ```
      template <typename ... a>
      void fun() {}

      int main() {
        fun<int, char, double>();
      }
      ```
    - template<形参列表> typename | class ... Args
      ```
      template <template<typename> class ... a>
      void fun() {}

      int main() {
        fun<template1, template2, template3>();
      }
      ```
  - 函数参数包(声明符的一种形式，出现于变参函数模版的函数形参列表中)
    - Args ... args
      ```
      template <typename... T>
      void fun(T... args) {}

      int main() {
        fun<int, char, double>();  // 编译报错，模版形参包的参数数量与函数形参的输入数量要一致
        fun<int, char, double>(4, 'c', 4.3);  // 编译通过
      }
      ```
  - 注意变长模版参数的位置
    - 在主类模版中，模版形参包必须是模版形参列表的最后一个形参
    - 在特化模版中，模版形参包无需是模版形参列表的最后一个形参
      ```
      template <typename... T>
      class C;

      template <typename T1, typename T2>
      class B;

      // 特化，合法
      template <typename... T, typename T2>
      class B<C<T...>, T2>{};
      ```
    - 在函数模版中，模版函数包可以在列表中稍早出现，只要其后所有的形参均可以从函数实参推导或者拥有默认实参即可
  - sizeof... (C++11)：获取形参包中参数的个数
    ```
    template <typename... T>
    void fun(T... args) {
      std::cout << sizeof...(T) << std::endl;
      std::cout << sizeof...(args) << std::endl;
    }
    ```

---

# 包展开 & 折叠表达式
- (C++11)通过包展开技术操作变长模版参数
  ```
  void fun() {}
  
  template <typename U, typename... T>
  void fun(U u, T... args) {
    std::cout << u << std::endl;
    fun(args...);
  }
  
  int main() {
    fun(1, 2, "hello", 'c');  // 递归调用，依次打印出“1”，“2”，“hello”，“c”
                              // 最后一次调用的fun是上面的无形参的fun函数
  }
  ```
- [(C++17)折叠表达式](https://zh.cppreference.com/w/cpp/language/fold)
  - 对C++11版本的包展开技术进行了简化
    ```
    template <typename... T>
    void fun(T... args) {
      ((std::cout << agrs << std::endl), ...);
    }

    int main() {
      fun(1, 2, "hello", 'c');  
    }
    ```
  - 多种格式的折叠表达式语法（见cppreference）
  - 折叠表达式用于表达式求值，无法处理输入（输出）是类型与模版的情况

---

# 完美转发 & lambda表达式模版
- (C++11)完美转发：std::forward函数
  ```
  void g(int&) {
    std::cout << "l-ref" << std::endl;
  }

  void g(int&&) {
    std::cout << "r-ref" << std::endl;  
  }

  template <typename T>
  void fun(T&& input) {  // 这里的T&&不是右值引用，是万能引用
                         // 当输入是左值的时候，会实例化为T&
                         // 当输入是右值的时候，会实例化为T&&(这里是表示右值引用的意思)
    g(input);  // l-ref，因为右值引用的变量是左值，但是这样就不合理了，因为我们传入的"3"是一个右值
    g(std::forward<T>(input));  // r-ref，加上forward后，配合T&&，就可以完美转发
  }

  int main() {
    fun(3);  
  }
  ```
  - 通常与万能引用结合使用
    ```
    template <typename T>
    void fun(T input) {
      g(std::forward<T>(input));
    }
    
    int main() {
      int x = 3;
      fun(x);  // r-ref，因为T input是拷贝传递，不是万能引用了，所以完美转发就不存在了(需要求证)
    }
    ```
  - 同时处理传入参数是左值和右值的情形
- (C++20)lambda表达式模版
  ```
  auto glambda = []<class T>(T a, auto&& b) { return a < b; };

  auto f = []<typename ...Ts>(Ts&& ...ts) {
    return foo(std::foward<Ts>(ts), ...);
  };
  ```

---

# 消除歧义
- 本质
  - 在模版当中用了一个依赖名称，而这个依赖名称依赖于模版形参可能有不同的解释
  - 如果这个依赖名称是个类型，就要加typename关键字进行描述
  - 如果这个依赖名称是个模版，就要加template关键字进行描述
- 使用typename表示一个依赖名称是类型而非静态数据成员
  ```
  template <typename T>
  void fun() {
    T::internal* p;  // 如果internal是一个类型，那这里就是定义一个指针
                     // 如果internal是一个静态成员变量，那这里就是乘法语句

    typename T::internal* p;  // 用typename明确表明这里的T::internal是一个类型，就没有歧义了
  }
  ```
- 使用template表示一个依赖名称是模版
  ```
  struct Str {
    template <typename T>
    static void internal() {}  // 静态成员函数

    void internal2() {}  // 一般成员函数
  };
  
  template <typename T>
  void fun() {
    T::internal<int>();  // 编译失败，编译器不能确定T::internal是否是一个变量，如果是的话这里的<会被认为是一个小于号
    T::template internal<int>();  // 编译成功，指明这里是一个模版

    T obj;
    obj.internal2<int>();  // 编译失败
    obj.template internal2<int>();  // 编译成功
  }
  
  int main() {
    fun<Str>();
  }
  ```

---

# 变量模版(C++14)
- demo 1
  ```
  template<typename T>
  T pi = (T)3.141592653;
  
  int main() {
    pi<float>;  // 3.14159
    pi<int>;  // 3
  }
  ```
- demo 2
  ```
  template<typename T>
  unsigned MySize = sizeof(T);
  
  template<typename T, unsigned v>
  unsigned EqSize = (sizeof(T) == v);

  int main() {
    MySize<float>;  // 4
    MySize<int>;  // 4

    EqSize<int, 4>;  // true
    EqSize<int, 2>;  // false
  }
  ```
