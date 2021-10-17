---
layout: wiki
title: Template Class
categories: [C++]
description: Template Class for C++
keywords: template, class, C++
---

# 基础
- 使用template关键字引入模版
  - 类模版的声明与定义——翻译单元的一处定义原则
  - 成员函数只有在调用时才会被实例化
    ```
    template <typename T>
    Class B{
      void fun(T input) {
        std::cout << input << std::endl;
      }

      auto fun2() {
        return B{};  // return B<T>{}的简化
      }
    };

    int main() {
      B<int> x;
      x.fun(3);

      B<Str> y;
      y.fun(Str{});  // 如果这行不加，编译通过，没有调用就不会实例化fun(Str input)函数
                     // 如果这行加了，编译不通过，Str类型没有重载标准输出
    }
    ```
  - 类内模版名称的简写(见fun2)
  - 类模版成员函数的定义
    - 类外
      ```
      template<typename T>
      class B {
      public:
        void fun();
      };

      template<typename T>
      void B<T>::fun() {
        // 上面的template<typename T>和<T>都不能省略
      }
      ```
    - 类内相对简单，不多说了

---

# 成员函数模板
- 类的成员函数模版
  ```
  class B {
  public:
    template <typename T>
    void fun() {}  // 类内

    template <typename T>
    void fun2();
  };

  template <typename T>
  void B::fun2() {}  // 类外

  int main() {
    B x;
    x.fun<int>();
    x.fun2<int>();
  }
  ```
- 类模版的成员函数模板
  ```
  template <typename T>
  class B {
  public:
    template <typename T2>
    void fun() {}  // 类内定义

    template <typename T2>
    void fun2();
  }

  template <typename T>
  template <typename T2>
  void B<T>::fun2() {}  // 类外定义

  int main() {
    B<int> x;
    x.fun<float>();
  }
  ```

---

# 友元函数（模版）
- 类（模版）的友元
  ```
  template <typename T>
  class B {
    friend auto operator + (B input1, B input2) {
      B res;
      res.x = input1.x + input2.x;
      return res;
    }
    int x = 3;
  };
  
  int main() {
    B<int> val1;
    B<int> val2;
    B<int> res = val1 + val2;
  }
  ```
- 可以声明一个函数模版为某个类（模版）的友元
  ```
  template <typename T2>
  void fun();  // 声明，用于类内的友元声明
  
  template <typename T>
  class B {
    template <typename T2>
    friend void fun();
  
    int x;
  };
  
  template <typename T2>
  void fun() {
    B<int> tmp1;
    tmp1.x;
    
    B<float> tmp2;
    tmp2.x;
  }
  ```
- C++11支持声明模版参数为友元
  ```
  template <typename T>
  class B {
    friend T;
  };
  ```

---

# 类模版的实例化
- 与函数实例化很像
- 可以实例化整个类，或者类中的某个成员函数

---

# 类模版的（完全）特化/部分特化（偏特化）
- 函数模版的特化不建议使用，但是类模版的特化不一样，是一个非常重要的计数
- 特化版本和基础版本可以有完全不同的实现
  ```
  // 完全特化
  template <typename T>
  struct B {
    void fun() {}
  };
  
  template <>
  struct B<int> {
    void fun2() {}  // 这里直接重新定义了一个新的函数
  };
  ```
- 部分特化
  ```
  template <typename T, typename T2>
  struct B {
    void fun() {}
  };
  
  template <typename T>  // 这里的T对应上面的T2
  struct B<int, T> {
    void fun2() {}
  }
  ```

---

# 类模版的实参推导(C++17开始)
- 基于构造函数的实参推导
  ```
  template <typename T>
  struct B {
    B(T input) {}  // 构造函数
  };
  
  int main() {
    B x(3);
  }
  ```
- 用户自定义的推导指引
- 注意，引入实参推导并不意味着降低了类型限制
  ```
  std::pair x(3, 3.14);  // std::pair<int, double> x(3, 3.14);
  x.first = "123";  // 报错，int类型变量不可以用string赋值
  ```
- C++17之前的解决方案：引入辅助模版函数
  ```
  template <typename T1, typename T2>
  std::pair<T1, T2> make_pair(T1 first, T2 second) {
    return std::pair<T1, T2>(first, second);
  }
  
  int main() {
    auto res = make_pair(3, 3.14);
  }
  ```
