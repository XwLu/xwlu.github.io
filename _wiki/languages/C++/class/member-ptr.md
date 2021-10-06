---
layout: wiki
title: Member Ptr of Class
categories: [C++]
description: member ptr for C++ class
keywords: member-ptr, class, C++
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
