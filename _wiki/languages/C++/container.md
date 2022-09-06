---
layout: wiki
title: Container 
categories: [C++]
description: Container for C++
keywords: container, C++
---

# 容器分类
- pair & tuple
- 序列容器：对象有序排列，使用整数值进行索引
- 关联容器：对像顺序不重要，利用键进行索引
- 适配器：调整原有容器行为，使其对外展现出新的类型、接口或返回新的元素
- 生成器：构造元素序列

---

# 迭代器
- 获取迭代器
  - begin, end
  - rbegin, rend
  - crbegin, crend
- 迭代器分类
  - Input Iterator
  - Output Iterator
  - Forward Iterator = Input Iterator + Output Iterator，只能往前挪
  - Bidirection Iterator，可以双向挪动
  - Random Access Iterator，支持iter+N
- 支持迭代器的容器统称为range

---

# pair & tuple
- tuple & tie
  - tuple
    - tuple是类似pair的模板
    - 每个pair都恰好有两个成员,tuple可以有任意数量的成员
    - 我们希望将一些数据组合成单一对象，但又不想麻烦地定义一个新数据结构来表示这些数据时，std::tuple是非常有用的。
    - std::tuple中元素是被紧密地存储的(位于连续的内存区域)，而不是链式结构。
    - 生成方式多样
      ```
      auto my_tuple0 = std::make_tuple("Peter", 10, "1024"};
      std::tuple<std::string, size_t, std::string> my_tuple1{"Mike", 20, "24"};
      std::tuple<std::string, size_t, std::string> my_tuple2{my_tuple0};
      ```
    - tuple 对象的成员函数 swap() 可以将它的元素和参数交换。
      ```
      my_tuple2.swap (my_tuple1);
      ```
    - 函数模板 get<>() 可以返回 tuple 中的一个元素的**引用值**。
      ```
      auto my_tuple = std::make_tuple (Name {"Peter","Piper"}, 42, std::string {"914 626 7890"});
      std::cout << std::get<0>(my_tuple)<< "age = "<<std::get<1>(my_tuple)<< " tel: " << std::get<2>(my_tuple) << std::endl;
      ```
    - 也可以用基于类型的 get<>() 从 tuple 获取元素，但要求 tuple 中只有一个这种类型的元素。
      ```
      auto my_tuple = std::make_tuple(Name{"Peter", "Piper"}, 42, std::string {"914 626 7890"});
      std::cout << std::get<Name>(my_tuple)<<" age = " << std::get<int> (my_tuple)<< " tel: " <<std::get<std::string>(my_tuple) << std::endl;
      ```
  - tie
    - tuple将几个不同类型的变量打包为一个对象，tie则负责将tuple类型的对象解构为几个变量
      ```
      tuple<int,double,string> t3 = {1, 2.0, "3"};
      int i; double d; string s;
      tie(i, d, s) = t3;
      tie(i, d, s) = {1, 2.0, "3"};//这一行会报错，因为tie只能解构tuple
      ```

---

# 序列容器
- array:元素个数固定的序列容器，不支持添加和删除
  - 定义时必须用**常量**指定array的大小，因为大小是模板参数之一，不可忽略
  - 当容器中的元素是连续存储的时候，容器都会有一个data()接口，返回指向第一个元素的指针
  - swap的实现是元素复制，效率很低
- vector:元素连续存储的序列容器
  - vector也有data()接口
  - swap是指针交换，效率很高
  - emplace_back()相比push_back()少了一次对象的拷贝或者移动，因此当对象是string或者自定义结构数据时，用emplace_back效率更高
    - (emplace_back vs push_back)[https://zhuanlan.zhihu.com/p/183861524]
  - insert和emplace是在vector中间插入元素，但效率很低；emplace和insert的差异与上面的emplace_back和push_back的差异一致
  - 会导致iter失效的操作：swap、push_back等写操作
- list:双向链表的容器
  - 插入和删除成本较低，随机访问成本较高
  - 支持pop_front, push_front操作，但是不支持[]访问
  - 提供了splice接口，A.splice(it, B)：将list B插入到A的it位置
  - 写操作通常不改变iter有效性
  - 如果你的程序有很多小的元素，且空间的额外开销很重要，则不要使用list或forward_list
- forward_list:基于单向链表的容器
  - 只支持递增，无rbegin
  - 不支持size 
  - 不支持push_back, pop_back
  - 提供了xxx_after操作 
- deque:vector与list的折衷，它会将整个容器分成若干段，段内是连续存储，段间是链表
  - push_back和push_front比较快
  - 在序列中间插入删除比较慢
  - 通常情况下我们不会想要用deque，随机访问速度不如vector，插入删除不如list
  - 只有在我们想要得到类似于vector的功能，但又希望push_front比较快的时候才会使用deque
- basic_string:提供了对字符串的专门支持
  - 提供了数值与字符串转换的接口
  - 短字符串优化，(short string optimization: SSO)[https://tigercosmos.xyz/post/2022/06/c++/sso/]

---

# 关联容器
- 按底层实现分为两类
  - set/map/multiset/multimap
    - 底层用红黑树实现
  - unordered_xxx
    - 底层用hash表实现
- set
  - 元素需要支持使用<比较大小，如果是自定义的元素，需要定义比较函数并在初始化的时候传入std::set<MyType, Cmp>
    ```
    #include <set>
    #include <type_traits>

    struct MyType {
        int x;
    };

    bool MyCmp(const MyType& lhs, const MyType& rhs) {
        return lhs.x < rhs.x;
    }

    int main() {
        std::set<MyType, decltype(&MyCmp)> s({MyType{1}, MyType{2}}, MyCmp);
        return 0;
    }
    ```
  - 插入insert/emplace/emplace_hint
    - emplace_hint可以给出一些插入的提示，从而加速插入的速度，但是如果hint给错了，反而增加耗时
      ```
      std::set<MyType, decltype(&MyCmp)> s({MyType{3}, MyType{5}}, MyCmp);
      s.insert(MyType{100});
      s.emplace(100);  // 两者等价，推荐使用emplace，减少拷贝和移动
      ```
  - 提供了extract来修改元素(C++17)，但是操作很复杂。
  - set的迭代器是只读的，不能用于修改元素。
- map
  - 每个节点是个std::pair，其中pair.first是const类型，不能修改
  - key需要支持使用<比较大小，也支持自定义比较函数
  - 支持k，v分别获取
    ```
    std::map<int, bool> m{{3, true}};
    for (auto& [k, v] : m) {
      std::cout << k << " " << v << std::endl;
    }
    ```
  - 访问元素：find/contain/[]/at
    - map.at(key)若key不存在，会抛出异常
    - map[key]若key不存在，会插入一个新的pair<key,T()>
- multiset/multimap
  - 允许重复key
  - 元素访问：
    - find：返回首个查到的元素
    - count：返回元素个数
    - lower_bound/upper_bound/equal_range：返回查找到的区间
      ```
      std::multiset<int> s{1, 3, 1};
      auto [b, e] = s.equal_range(100);
      for (auto iter = b; iter != e; ++iter) {
        // ...
      }
      auto b = s.lower_bound(100);
      auto e = s.upper_bound(100);
      ```
- unordered_xxx
  - 底层实现：
    - 新建一个size=N的bucket vector
    - 对于新插入的元素，将其key转换为hash值n，index = n % N，往bucket vector[index]中维护的链表的尾部插入该元素
    - 每一个bucket中都维护了一个链表，一般来说大部分bucket中的链表元素的长度都比较小
  - 与set,map相比，查找性能更好，转换成hash后找对应的bucket，如果其中的链表元素个数是1，直接返回；即使大于1，也一般都比较小，做几次判断即可。
  - 插入操作一些情况下会慢，比如一个bucket中包含的链表元素过多了，就会出发rehash，这个过程就比较耗时了
  - key需要支持两种操作
    - 转换为hash
    - 判等
  - 除了!=和==，不支持容器级别的关系运算；同时!=和==的计算很慢，因为当一个bucket中的链表元素较多时，为了判断两个set是否相等，需要将两条链表判断是否相等
  - 自定义hash转换
    - 方法1
      ```
      struct Str {
        int x;
      };
  
      size_t MyHash(const Str& val) {
          return val.x;
      }
  
      bool MyEqual(const Str& lhs, const Str& rhs) {
        return lhs.x = rhs.x;
      }
      std::unordered_set<Str, decltype(&MyHash), decltype(&MyEqual)> s{1, MyHash, MyEqual};  // 1 是bucket vector的size，最小为1
      ```
    - 方法2(推荐)
      ```
      class Str {
        int x;
        bool operate==(const Str& s) const {
          return (this->x == s.x);
        }
      };

      class MyHasFunction {
      public:
        size_t operator(const Str& s) const {
          return s.x;
        }
      };

      std::unordered_set<Str, MyHasFunction> s;
      ```

---

# 适配器
- 类型适配器
  - basic_string_view (C++17)
    - 代码demo
      ```
      void fun(std::string_view str) {  // 这里不需要引用，因为string_view只记录了string开头和结尾的位置，无论字符串本身有多长，它的内存都很小
        if (!str.empty()) {
          std::cout << str[0] << std::endl;
        }
      }

      fun("1234");  // fun(char[6])
      fun(std::string("1234"));  // fun(std::string)

      std::string s("12345");
      fun(std::string_view(s.begin(), s.begin() + 3));  // 只传入"123"
      ```
    - 提供较低成本的操作接口
      - 比如std::string的substr会开辟一段新的内存来存放截取到的字符串，但std::string_view的substr只会初始化一个新的string_view来记录截取的字符串，string_view只占16个字节，所以很轻量
    - 不能进行写操作
    - 一般string_view只作为函数的输入，作为输出的时候需要小心，函数中的临时变量销毁导致string_view记录的指针位置失效
  - span (C++20)
    - span就是string_view的功能在其他类型的容器上的扩展，用于提升代码的性能
    - 只能处理连续存储的容器，比如vector、array
    - 支持写操作，这与string_view不同
- 接口适配器
  - stack
    - stack中维护了一个底层容器，然后封装了它的接口，只保留了push，pop，top这三个操作
      ```
      std::stack<int> s;
      std::stack<int, std::vector<int>> s;  // 指定使用vector作为stack的底层容器
      ```
  - queue
  - priority_queue
    - 输入的元素需要支持比较操作（比较操作用于确定优先级）
    - 支持自定义比较函数
    - 输入元素和queue一致，但输出的元素一定是所有元素中优先级最高的元素
- 数值适配器(C++20)
  - std::ranges::XXX_view, std::ranges::views::XXX, std::views::XXX
  - 可以将一个输出区间中的值变换后输出
    - demo1
      ```
      std::vector<int> v{1, 2, 3, 4, 5};


      int Square(int i) {
        return i * i;
      }
      for (auto p : std::ranges::transform_view(v, Square)) {
        std::cout << p << " ";  // 打印出1, 4, 9, 16, 25
      }
      std::cout << std::endl;


      bool IsEven(int i) {
        return i % 2 == 0;
      }
      // 用法1
      for (auto p : std::ranges::filter_view(v, isEven)) {
        std::cout << p << " ";  // 只打印出偶数
      }
      std::cout << std::endl;

      // 用法2
      auto x = std::views::filter(IsEven);
      for (auto p : x(v)) {
        std::cout << p << " ";  // 只打印出偶数
      }
      std::cout << std::endl;

      // 用法3
      auto x2 = std::views::filter(IsEven);
      auto y = std::views::transform(Square);
      for (auto p : v | x2 | y) {  // 按位或，模拟linux bash中的pipe功能，这里可以无限后缀新操作 v | x2 | y | k
        std::cout << p << " ";
      }
      std::cout << std::endl;
      
      // 用法4
      auto operate = std::views::filter(IsEven) ｜ std::views::transform(Square);
      for (auto p : v | operate) {
        std::cout << p << " ";
      }
      std::cout << std::endl;
      ```
  - 数值适配器可以组合，引入复杂的数值适配逻辑
  - view这种算法和之前讨论的泛型算法有两点核心区别
    - view没有对输入的东西立即进行计算，而是需要的时候才进行计算, 这样可以提高性能（比如10000个数据，只有前几个元素会被view计算，后面的计算就省掉了）
    - view模糊了算法和容器的概念，它可以被称作容器，也可以被称作算法
    - 配合ranges还可以灵活组织程序逻辑
- 生成器(C++20)
  - std::ranges::itoa_view
    ```
    for (int i : std::ranges::itoa_view{1, 10}) {
      std::cout << i << " ";
    }
    std::cout << std::endl;

    for (int i : std::views::itoa(1, 10)) {
      std::cout << i << " ";
    }
    std::cout << std::endl;

    // std::views::itoa(...)，如果这里的...只有一个元素，就表示生成一个以该元素为开头的无限长的容器，后面的take(n)表示取容器中的前9个元素
    for (int i : std::views::itoa(1) | std::views::take(9)) {
      std::cout << i << " ";
    }
    std::cout << std::endl;

    // 上面三种写法的输出都是1，2，3，4，5，6，7，8，9
    ```

---

# 注意事项
- 尽量使用at方法来访问元素
  - 运算符\[\]不会对索引值进行检查，像调用myarray\[-1\]是不会报错的
  - 使用at()，将在运行期间捕获非法索引，默认将程序中断
