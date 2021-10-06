---
layout: wiki
title: Class
categories: [C++]
description: class for C++
keywords: class, C++
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
