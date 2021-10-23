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
- 变长模版(Variadic Template)
  - 形参包
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

    ```
