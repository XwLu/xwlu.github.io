---
layout: wiki
title: Template Function
categories: [C++]
description: Template function for C++
keywords: template, function, C++
---

# 使用template关键字引入模板
- 函数模板的声明与定义
  ```
  // 声明
  template<typename T>
  void fun(T);

  // 定义
  template<typename T>
  void fun(T input){
    std::cout << input << std::endl;
  }
  ```
- typename关键字可以替换为**class**，含义相同
- 函数模板中包含了两对参数：函数形参(上面的input)/实参；模版形参(上面的T)/实参
  - **注意**：函数模版不是函数(不能调用)，我们需要在**编译期**给模版形参赋值相应的实参，才能把函数模版实例化成一个函数(可以去cppinsights里面去看看上面这份代码被预处理后的样子)
  - 函数形参是**运行期**赋值的

---

# 函数模板的显示实例化
```
fun<int>(3);  // 会在终端打印出“3”
```
- 编译期的两阶段处理
  - 模板语法检查
    ```
    // 第一阶段只会检查下面这段代码有没有语法错误
    template<typename T>
    void fun(T input){
      std::cout << input << std::endl;
    }
    ```
  - 模板实例化
    ```
    struct Str {};
    // 第二阶段会实例化对应的函数，这里会实例化出来两个函数，其中第二个函数会报错，因为Str没有重载<<运算符
    fun<int>(3);
    fun<Str>(Str{});
    ```
- 模板必须在实例化时可见——模板函数只需要满足**翻译单元**的一处定义原则(一般函数需要满足**程序**的一处定义原则)
- 注意与内联函数的异同
  ```
  // header.h
  template<typename T>
  void fun(T input){
    std::cout << input << std::endl;
  }

  inline void normal_fun() {}

  // main.cc
  #include "header.h"

  // source.cc
  #include "header.h"
  ```
  - 上面的代码如果normal_fun不加inline，编译会报错重复定义，因为normal_fun需要满足程序的一处定义原则，加上inline后只需要满足翻译单元的一处定义原则即可
  - 模板函数本身就只需要满足翻译单元的一处定义原则，所以不需要加inline
  - 当然，模板函数也可以加inline，作用是告诉编译器在实例化函数的时候，顺便替换掉函数的调用，直接把函数逻辑嵌入到实例化的位置

---

# 函数模板的重载
```
template<typename T>
void fun(T input){
  std::cout << input << std::endl;
}

template<typename T>
void fun(T* input){
  std::cout << *input << std::endl;
}

template<typename T, typename T2>
void fun(T input, T2 input2){
  std::cout << input << std::endl;
  std::cout << input2 << std::endl;
}

int x = 3;
fun<int>(&x);
fun<int>(x);
```

---

# 模版实参的类型推导
- 如果函数模版在实例化时没有显示制定模版实参，那么系统会尝试进行推导
- 推导时基于函数实参（表达式）确定模版实参的过程，其基本原则与auto类型推导类似
  - 函数形参是左值引用/指针：
    - 忽略表达式类型中的引用
    - 将表达式类型与函数形参模式匹配以确定模版实参
      ```
      template<typename T>
      void fun(T& input) {
        std::cout << input << std::endl;
      }
      
      int main() {
        int x = 3;
        fun(3);  // fun(int&);

        int& y = x;
        fun(y);  // fun(int&);

        const int& z = x;
        fun(z);  // fun(const int&);
      }
      ```
  - 函数的形参是万能引用
    - 如果实参表达式是右值，那么模版形参被推导为去掉引用的基本类型
    - 如果实参表达式是左值，那么模版形参被推导为左值引用，触发引用折叠
      ```
      template<typename T>
      void fun(T&& input) {}  // 如果T是一个确定的类型（比如int&&，double&&），那&&就是右值引用
                              // 编译器在编译的时候，T还没被确定为一个具体的类型，那&&就被当作万能引用
                              // 既可以引用左值，也可以引用右值
      int main() {
        int&& x = 3;
        fun(3);  // fun(int&&)
        int y = 3;
        fun(y);  // fun(int&)
      }
      ```
  - 函数形参不包含引用(写起来最简单，模型推导最复杂)
    - 忽略表达式中的引用
    - 忽略顶层const
    - 数组、函数转换成相应的指针类型
      ```
      template<typename T>
      void fun(T input) {}
      
      int main() {
        fun(3);  // fun(int)

        int x = 3;
        int& ref = x;
        fun(ref);  // fun(int)

        const int& ref = x;
        fun(ref);  // fun(int)

        const int* const ptr = &x;  // 注意，这里的顶层const是后面那个const，直接修饰变量的那个
                                    // 因为变量是拷贝进fun的，所以这个const会失效
        fun(ptr);  // fun(const int*);

        int x[3];
        fun(x);  // fun(int*)
      }
      ```
  - 多个T情况
    ```
    // 同样的T
    template<typename T>
    void fun(T input1, T input2) {}

    int main() {
      fun<int>(3, 5.0);  // 通过，不存在模型推导，T显示指定为int
      fun(3, 5.0);  // 不通过，3推导出T为int，5.0推导出T为double，int和double不是一个类型，报错
    }

    // 不同的T
    template<typename T>
    void fun(T1 input1, T2 input2) {}

    int main() {
      fun<int>(3, 5.0);  // 通过，T1显示指定为int，T2隐式指推断为double
    }
    ```

---

# 模版实参并非总是能够推导得到
- 如果模版形参与函数形参无关，则无法推导
  ```
  template <typename T, typename Res>
  Res fun(T input) {}

  int main() {
    fun(3);  // Res无法推导，编译不通过
  }
  ```
- 即使相关，也不一定能进行推导，推导成功也可能存在因歧义而无法使用(见上面“同样的T”)
  ```
  template <typename T>
  void fun(typename std::remove_reference<T>::type input) {}

  int main() {
    fun(3);  // 编译器在实例化fun函数的时候，发现std::remove_reference<T>::type的结果是int
             // T到底是啥不确定，可以是int，也可以是int&，int&&所以编译器报错
  }
  ```

---

# 在无法推导时，编译器会选择使用缺省模版实参
```
template <typename T = int>
void fun(typename std::remove_reference<T>::type input) {}

int main() {
  fun(3);  // 编译通过，有缺省值
}
```
- 只能处理推导不成功的场景，不能处理歧义而无法使用的场景
  ```
  template<typename T = double>
  void fun(T input1, T input2) {}

  int main() {
    fun(3, 5.0);  // 不通过，当编译器可以从变量推导T的类型的时候，就不会看T的缺省值
  }
  ```
- 可以为任意位置的模版形参指定缺省模板实参——注意与函数缺省实参的区别
  - 函数的缺省实参只能排在最后面
    ```
    void fun(int x = 3.0, int y) {}  //  编译不通过

    template <typename Res = double, typename T>  // 编译通过
    Res fun(T input) {}

    int main() {
      fun(3);
    }
    ```

---

# 显示指定部分模版实参
- 显示指定的模版实参必须是从最左边开始，依次指定
- 模版形参的声明顺序会影响调用的灵活性
- 越是需要显示指定的T，就越要放在前面
  ```
  // good case
  template <typename Res, typename T>
  Res fun(T x) {}

  int main() {
    fun<int>(5);  // Res显示指定，T隐式指定
  }

  // bad case
  template <typename T, typename Res>
  Res fun(T x) {}

  int main() {
    fun<int>(5);  // T显示指定, Res无法推断
  }
  ```

---

# 函数模板自动推导是会遇到的几种情况
- 函数形参无法匹配——SFINAE（替换失败并非错误）
  ```
  template <typename T>
  void fun(T x, T y) {}

  int main() {
    fun(3, 5.0);  // 编译不通过，但是不代表template fun有错误，只是找不到合适的匹配模板
                  // 如果重载一个fun(T1, T2)，就没问题了
                  // SFINAE这个概念在元编程的时候很有用
  }
  ```
- 模板与非模版同时匹配，匹配等级相同，系统会选择非模版函数
- 多个模版同时匹配，此时采用偏序关系确定选择“最特殊”版本
  ```
  template <typename T>
  void fun(T x, float y) {
    std::cout << 2 << std::endl;
  }

  template <typename T, typename T2>
  void fun(T x, T2 y) {
    std::cout << 1 << std::endl;
  }

  template <typename T>
  void fun(T* x, float y) {
    std::cout << 3 << std::endl;
  }

  int main() {
    fun(3, 5.0);  // 2 
    int x = 3;
    fun(&x, 5.0);  // 3
  }
  ```
  - 如果一样特殊，那就编译不通过了
    ```
    template <typename T, typename T2>
    void fun(T* x, T2 y) {}

    template <typename T, typename T2>
    void fun(T x, T2* y) {}

    int main() {
      int x = 3;
      fun(&x, &x);
    }
    ```

---

# 模版函数的实例化控制
- 只实例化，不调用函数
  - 某些库中不想给出模版的内部实现逻辑，只给出模版的声明，此时就需要提前实例化用户想要使用的函数
- 显示实例化定义
  ```
  // header.h
  template <typename T>
  void fun(T x) {
    std::cout << x << std::endl;
  }
  
  template
  void fun<int>(int);  // 实例化
  // 也可以这么写：void fun(int);

  // main.cc
  #include "header.h"
  int main() {
    int x = 3;
    fun<int>(x);
  }
  ```
- 显示实例化声明
  ```
  //header.h
  template <typename T>
  void fun(T x) {
    std::cout << x << std::endl;
  }

  // source.cc
  #include "header.h"

  template
  void fun<int>(int);

  // main.cc
  #include "header.h"

  extern template
  void fun<int>(int);  // extern表示已经在别的翻译单元实例化过了，这里不要再实例化一遍
                       // 链接的时候会直接链到source里面的实例

  int main() {
    int x = 3;
    fun<int>(x);
  }
  ```
- 注意一处定义原则
  - 隐式实例化可以在多处有实例化，编译器会选择其中一个，删除掉多余的
    ```
    // header.h
    template <typename T>
    void fun(T x) {
      std::cout << x << std::endl;
    }

    // source.cc
    #include "header.h"

    void fun2() {
      fun<int>(3);  // source中隐式实例化一次
    }

    // main.cc
    #include "header.h"

    int main() {
      int x = 3;
      fun<int>(x);  // main中隐式实例化一次
    }
    ```
  - 显示实例化原则上在整个程序中只能有一处，但是有多处的话，编译器也不一定会报错，尽量不要这么写
    ```
    // header.h
    template <typename T>
    void fun(T x) {
      std::cout << x << std::endl;
    }

    // source.cc
    #include "header.h"

    template
    void fun<int>(int);  // source中显示实例化一次

    void fun2() {
      fun<int>(3);  
    }

    // main.cc
    #include "header.h"

    template
    void fun<int>(int);  // main中显示实例化一次

    int main() {
      int x = 3;
      fun<int>(x);
    }
    ```
- 注意实例化过程中的模版形参推导
  ```
  // 模版1
  template <typename T>
  void fun(T x) {
    std::cout << x << std::endl;
  }

  template
  void fun(int* x);  // 显示实例化放在这里会调用模板1，因为程序从上到下执行，还没看到后面的模板2
  
  // 模板2
  template <typename T>
  void fun(T* x) {
    std::cout << x << std::endl;
  }

  template
  void fun(int* x);  // 显示实例化放在这里会调用模板2
  ```

---

# 模版函数的（完全）特化
- 模版函数不支持部分特化
- 针对某种模版参数，引入特别的函数版本
  ```
  template <typename T>
  void fun(T x) {
    // 如果T的实例化类型不是int，执行这里的逻辑
  }
  
  template <>
  void fun(int x) {
    // 如果传入的参数类型是int，执行这里的逻辑
  }
  ```
- 注意与函数模版重载的差异，特化并不引入新的（同名）名称，只是为某个模版函数针对特定的模版实参提供优化算法
- 避免使用特化
  - 特化不参与重载解析，会产生反直觉的效果
  - 通常使用重载来代替，直接实现相应的函数就行了，比如想对int型的入参引入逻辑优化，直接实现void fun(int)
  - 有些情况无法通过重载来实现
    ```
    template <typename T, typename Res>
    Res fun(T input) {}
    // 上面这个模板无法实现对应的重载函数，因为Res不在参数列表里，是返回值的类型，Res无法被重载
    ```
    - 使用if constexpr来解决
      ```
      template <typename T, typename Res>
      Res fun(T input) {
        if constexpr(std::is_same_v<Res, int>) {
          // ...
        } else {
          // ...
        }
        return Res;
      }
      ```
    - 引入“假”函数形参
      ```
      template <typename T, typename Res>
      Res fun(T inputi, const Res&) {
        std::cout << 1 << std::endl;
        return Res{};
      }  // 引入一个Res，函数里不使用

      template <typename T>  // 注意，<>里面是有东西的，所以这是重载，不是特化
      int fun(T input, const int&) {
        std::cout << 2 << std::endl;
        return int{};
      }
      
      int main() {
        int x = 0;
        fun(&x, int{});  // 2
      }
      ```
    - 通过类模版特化来解决问题

---

# 补充
- (C++20)函数模板的简化形式：使用auto定义模板参数类型
  - 优势：书写简洁
  - 劣势：在函数内部需要间接获取参数类型信息
