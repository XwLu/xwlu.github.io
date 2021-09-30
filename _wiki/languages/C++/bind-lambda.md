---
layout: wiki
title: Bind & Lambda 
categories: [C++]
description: Lambda for C++
keywords: bind, lambda, C++
---

[优秀教程](https://www.jianshu.com/p/d686ad9de817)

### 背景
- 很多算法允许通过可调用对象自定义计算逻辑的细节
  - transform / copy_if / sort
- 可调用对象
  - 函数指针：概念直观，但是定义位置受限

    ```
    #include <functional>
    bool BiggerThan(const int val) {
      return val > 3;
    }
    int main() {
      std::vector<int> x{0, 1, 2, 3, 4};
      std::vector<int> y;
      // 不能把BiggerThan的定义写在这里，因为C++不支持在函数中定义函数
      std::copy_if(x.begin(), x.end(), std::back_inserter(y), BiggerThan);  // y = {4};
    }
    ```
  - 类：功能强大，但是书写麻烦
  - bind：基于已有的逻辑来灵活适配，但描述复杂逻辑时愈发可能会比较复杂难懂
  - lambda表达式：小巧灵活，功能强大

### Bind
- bind：通过绑定的方式修改可调用对象的调用方式(C++11)

  ```
  #include <functional>
  bool BiggerThan(const int val1, const int val2) {
    return val1 > val2;
  }
  int main() {
    // demo1
    using namespace std::placeholders;
    std::vector<int> x{0, 1, 2, 3, 4};
    std::vector<int> y;
    // 不能把BiggerThan的定义写在这里，因为C++不支持在函数中定义函数
    std::copy_if(x.begin(), x.end(), std::back_inserter(y), std::bind(BiggerThan, _1, 3));  // y = {4};
    
    // demo2
    auto f = std::bind(BiggerThan, _1, 3);
    std::cout << f(50) << std::endl;  // 1
    
    // demo3
    auto f = std::bind(BiggerThan, _2, _1);
    std::cout << f(3, 2) << std::endl;  // 0

    // demo4
    int i = 0;
    auto b = std::bind(Proc, i);
    b();
    std::cout << i << std::endl;  // 0

    b = std::bind(Proc, std::ref(i));
    b();
    std::cout << i << std::endl;  // 1
  }
  ```
- bind_front：std::bind的简化形式(C++20)

### Lambda
- #### 背景
- 《C++ lambda story》建议阅读
- 为了更灵活地实现可调用对象而引入
- C++11 ～ C++20持续更新
  - C++11引入lambda表达式
  - C++14支持初始化捕获、泛型lambda
  - C++17引入constexpr lambda、\*this捕获
  - C++20引入concepts、模版lambda
- lambda表达式会被C++翻译成类进行处理

- #### 基本组成
- 参数与函数体
- 返回类型

  ```
  auto x = [](int val) {
    if (val > 3) {
      return 1.0;
    } else {
      return 1.3f;
    }
  };
  // 上面的代码编译会报错，因为1.0和1.5f不是同一个类型的值，无法自动推导，需要改成下面的显示指定返回类型
  auto x = [](int val) -> double {
    if (val > 3) {
      return 1.0;
    } else {
      return 1.3f;
    }
  };
  std::cout << x(5) << std::endl;
  ```
- 捕获：
  - 针对函数体中使用的局部自动对象进行捕获; 局部静态对象或者全局对象是不需要捕获的，可以直接在lambda内调用
  - 值捕获、引用捕获与混合捕获(C++11)
    - [=]：将所有局部自动对象值捕获；[&]：将所有局部自动对象引用捕获 
    - [&, z]：除了z之外的局部自动对象引用捕获，z值捕获
    - [&x, z]：x引用捕获，z值捕获
  - this捕获(C++11)
  
    ```
    struct Str {
      auto fun() {
        int val = 3;
        // auto lam = [val, x] () {  // 编译失败，因为x在这里不是局部自动变量
        // auto lam = [val] () {  // 编译失败，因为不是全局变量或者局部静态变量
        auto lam = [val, this] () {  // 编译成功, this指向了Str的某个对象实例
          return val > x;
        }
        return lam();
      }
      int x;
    };
    ```
  - 初始化捕获(C++14)
  
    ```
    int x = 3;
    int y = 4;
    // method1
    auto lam = [z = x + y](int val) {
      return val > z;
    };
    // method2
    auto lam = [x, y] (int val) {
      return val > x + y;
    }
    // method1的好处在于x+y这步计算不用每次调用lam的时候都重新算一遍
    ```
  - \*this捕获(C++17)

    ```
    struct Str {
      auto fun() {
        int val = 3;
        auto lam = [val, this] () {
        // auto lam = [val, *this] () {
          return val > x;
        };
        return lam;
      }
      int x;
    };

    auto wrapper() {
      Str s;
      return s.fun();
    }

    int main() {
      auto lam = wrapper();  // wrapper执行完之后，临时变量“s”被销毁
      lam();  // 此时的lam包含了一个悬挂指针，指向了一个被销毁的对象，运行的结果就是未定义的
      // 解决方式就是将this捕获改成上面的*this捕获，直接将对象的内容复制过来，好处是更加安全，坏处是复制对资源的消耗较大
    }
    ```
  - 说明符
    - mutable

      ```
      int main() {
        int y = 3;
        auto lam = [y] () {
          ++y;  // 编译报错，因为编译器会将lambda表达式构造为一个类，而lam函数会被加上const修饰符，因此无法对类内部的成员变量进行修改
          return val > y;
        }
        // 解决方案是加上mutable说明符
        auto lam = [y] () mutable {
          // ...
        }
      }
      ```
    - constexpr(C++17)

      ```
      auto lam = [](int val) constexpr {
        return val + 1;
      }
      constexpr int val = lam(100);  // constexpr表示在编译期就可以确定值是多少
      ```
    - consteval(C++20)
      - constexpr所修饰的函数，既可以在编译期调用，也可以在运行期调用
      - consteval修饰的函数只能在编译期调用
  - 模板形参(C++20)

    ```
    auto lam = []<typename T>(T val) {
      return val + 1;
    }
    constexpr int val = lam(100);
    constexpr int val = lam(100.0);
    ```
- #### lambda深入
  - 捕获时计算(C++14)：就是上面举的初始化捕获的例子
  - 即调用函数表达式(Immediately-Invoked Function Expression, IFE)
  
    ```
    int x = 3, y = 5;
    const auto val = [z = x + y]() {
      return z;
    }();  // 构造完立刻执行该lambda表达式
    ```
  - 使用auto避免复制(C++14)
  
    ```
    std::map<int, int> m{{2, 3}};
    auto lam = [](const std::pair<int, int>& p) {
      return p.first + p.second;
    };
    std::cout << lam(*m.begin()) << std::endl;
    // 上面的case，*m.begin()的返回值类型是std::pair<const int, int>
    // 因此，系统在这里无法执行引用，而是进行了复制操作(隐式类型转换)
    // 解决方案1：auto lam = [](const std::pair<const int, int>& p) {/*...*/};
    // 解决方案2：auto lam = [](const auto& p) {/*...*/};
    ```
- Lifting(C++14)

  ```
  auto fun(int val) {
    return val - 1;
  }

  auto fun(doule val) {
    return val - 1;
  }

  int main() {
    auto b = std::bind(fun, 3);  // 编译失败，因为bind无法区分调用哪个fun函数
    b();

    auto lam = [](auto x) { return fun(x); };
    lam(3);  // 4
    lam(3.5);  // 4.5
  }
  ```
- 递归调用(C++14)

  ```
  // demo 1
  int factorial(int n) {
    return n > 1 ? n * factorial(n - 1) : 1;
  }
  int main () {
    factorial(5);
    // 上面的代码是合法的，因为编译器看到“int factorial(int n)”的时候就已经知道了这个函数的输入输出信息，这就够了
  }

  // demo 2
  int main() {
    auto factorial = [](int n) {
      return n > 1 ? n * factorial(n - 1) : 1;
    };
    // 上面的代码无法编译通过，因为编译器要理解factorial函数（输出输出信息）就需要先解析auto的值
    // auto的值需要 = 后面内容来确定，但是 = 后面的内容中的factorial这个函数的行为和返回值目前又是未知的
    // 变成了鸡生蛋，蛋生鸡的问题
  }

  // demo 3
  int main() {
    auto factorial = [](int n) {
      auto f_impl = [](int n, const auto& impl) -> int {  //这里的->int一定不能少，没有这个的话，编译器就会尝试解析下面一行的返回类型用于确定f_impl的类型。但下面一行的impl的返回类型又是未知的，所以就编译器就无法编译了
        return n > 1 ? n * impl(n - 1, impl) : 1;
      };
      return f_impl(n, f_impl);
    };
    factorial(5);
    // 上面的代码是合法的，解析如下
    // 首先编译器同样不知道factorial的类型，所以需要解析第一个 = 后面的内容
    // 而第一个 = 后面的内容没有出现factorial，这就意味着鸡蛋问题不存在，后面的内容是可以正常解析的
    // 接下来分析factorial内部如何解析
    // 首先编译器需要解析f_impl的类型，而f_impl = 后面的内容中没有出现f_impl，也没有鸡蛋问题，所以后面的内容也是可以正常定义的
    // 编译器在定义f_impl的时候并不需要知道后面传入的impl是个啥类型，反正auto字段会被解析为template，只有后面调用的时候才会实例化，才会确定具体的函数类型
    // 由于前面的f_impl已经定义好了，所以f_impl(n, f_impl)也没啥问题，顺理成章
    // 整个这段代码是所以能成功，关键就是我们使用了C++14中的lambda支持auto这个特性，否则就不能将impl定义成一个模板函数
    // 如果不用auto，还是有其他方法可以做的，参考C++ lambda story这本书
  }
  ```
