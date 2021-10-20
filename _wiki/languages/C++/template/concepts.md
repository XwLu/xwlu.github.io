---
layout: wiki
title: Concepts
categories: [C++]
description: Concepts for C++
keywords: concepts, C++
---

# 背景
- 模版的问题：没有很好的方法对模版参数引入相应的限制
  ```
  template <typename T>
  void fun() {
    std::cout << T << std::endl;  // 这一行是否能通过编译需要通过阅读代码才能判断出来
  }
  ```
  - 参数能否正常工作，通常需要阅读代码才能理解
  - 编译报错友好性较差(vector<int&>报错很离谱)
- 为了解决上面的问题，C++20引入了concepts机制
  - 编译期谓词，基于给定的输入，返回true或false
  - 与constraints(require从句)一起使用限制模版参数
  - 通常置于表示模版形参的尖括号后面进行限制
    ```
    template <typename T>
    // IsAvail是在编译期求值的
    concept IsAvail = std::is_same_v<T, int> || std::is_same_v<T, float>;

    std::cout << IsAvail<int> << std::endl;  // 1
    std::cout << IsAvail<char> << std::endl;  // 0

    template <typename T>
      requires IsAvail<T>
    void fun(T input) {
      // ...
    }

    int main() {
      fun(3);  // 编译通过
      fun(true);  // 编译不通过，报错内容很少，很清晰
    }
    ```

---

# 定义与使用
- 包含一个模版参数的concept
  - 使用requires从句(见上面的代码)
  - 直接替换template(更加清晰)
    ```
    template <typename T>
    concept IsIntOrFloat = std::is_same_v<T, int> || std::is_same_v<T, float>;

    template <IsIntOrFloat, T>
    void fun(T input) {}
    ```
- 包含多个模版参数的concept
  ```
  template <typename T1, typename T2>
  concept IsDiff = !std::is_same_v<T1, T2>;

  template <typename T1, typename T2>
    require IsDiff<T1, T2>
  void fun(T1 input1, T2 input2) {}

  int main() {
    fun(3, 5);  // 编译失败，类型相同
  }
  ```
  - 用作类型constraint时，少传递一个参数，推导出的类型将作为首个参数
    ```
    template <class T, class U>
    concept Derived = std::is_base_of<U, T>::value;

    // 这里的Base是程序中前面定义的一个类 
    template<Derived<Base>, T>  // 这里会把T当作Derived的第一个参数
    void f(T);  //  T由Derived<T, Base>约束，也就是说T是Base的派生类则返回真
    ```

