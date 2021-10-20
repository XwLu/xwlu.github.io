---
layout: wiki
title: Requires & Concepts
categories: [C++]
description: Requires & Concepts for C++
keywords: requires, concepts, C++
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
    requires IsDiff<T1, T2>
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

---

# requires表达式
- 区分requires表达式和requires从句
  - 表达式
    ```
    template <typename T>
    concept Addable = requires(T x) { x + x; };
    ```
  - 从句
    ```
    template <typename T>
      requires Addable<T>
    T add(T x, T y) { return x+ y; };
    ```
  - 从句+表达式
    ```
    template <typename T>
      requires { requires(T x) { x + x; } }  // 这里的表达式只有一行，所以最外面的大括号可以省略
    T add(T x, T y) { return x+ y; };
    ```
- 分类
  - 简单要求
    ```
    template <typename T>
    concept Addable = requires (T a, T b) {
      a + b;  // T类型的数据需要满足可加性
    };

    template <Addable T>
    auto fun(T x, T y) {
      return x + y;
    }

    int main() {
      fun(3, 4);  // 通过
      fun(3, "1223");  // 不通过
    }
    ```
  - 类型要求
    ```
    template <typename T>
    concept Avail = requires {
      typename T::inner;  // 要求T::inner是一个合法类型
    };

    template <Avail T>
    auto fun(T x) {}

    struct Str {
      using inner = int;
    };

    int main() {
      fun(3);  // 不通过
      fun(Str{});  // 通过
    }
    ```
  - 复合要求
    ```
    template <typename T>
    concept Avail = requires (T x) {
      {x + 1} -> int;  // 1.x需要支持+1操作；2.x+1的结果需要可以转换为int类型
    };

    template <Avail T>
    auto fun(T x) {}

    struct Str {};

    int main() {
      fun(3);  // 通过
      fun(Str{});  // 不通过
    }
    ```
  - 嵌套要求
- requires从句会影响重载解析与特化版本的选取
  - 只有requires从句有效且返回为true时相应的模板才会被考虑
    ```
    // 如果没有requires，下面两个模版是一样的，编译器不允许这种存在
    // 引入requires后，系统会选择requires返回为true的模版
    template <typename T>
      requires std::is_same_v<T, float>
    void fun(T) {
      std::cout << 1 << std::endl;
    }
    
    template <typename T>
      requires std::is_same_v<T, int>
    void fun(T) {
      std::cout << 2 << std::endl;
    }
    
    int main() {
      fun (3);  // 2
    }
    ```
  - requires所引入的限定具有偏序特性，系统会选择限制最严格的版本
  ```
  template <typename T>
  concept C1 = std::is_same_v<T, int>;

  template <typename T>
  concept C2 = std::is_same_v<T, int> || std::is_same_v<T, float>;

  template <C1, T>
  void fun(T) {
    std::cout << 1 << std::endl;
  }

  template <C2, T>
  void fun(T) {
    std::cout << 2 << std::endl;
  }

  int main() {
    fun(3);  // 1
  }
  ```
- 特化小技巧：在声明中引入"A||B"进行限制，之后分别对A和B引入特化
  ```
  template <typename T>
    requires std::is_integral_v<T> || std::is_floating_point<T>
  class B;

  // 完全特化
  template<>
  class B<int> {};
  
  template<>
  class B<float> {};
  
  // 部分特化（偏特化）
  template <typename T>
    requires std::is_integral_v<T>
  class B<T> {};  // int型偏特化
  
  template <typename T>
    requires std::is_floating_point<T>
  class B<T> {};  // 浮点型偏特化
  
  int main() {
    B<double> x;  // 完全特化版本编译不通过，因为没有double类型的完全特化  
                  // 部分特化版本编译通过，有浮点型偏特化
  }
  ```
