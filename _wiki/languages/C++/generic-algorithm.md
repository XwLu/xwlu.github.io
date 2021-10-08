---
layout: wiki
title: Generic Algorithm
categories: [C++]
description: generic algorithm for C++
keywords: generic algorithm, C++
---

# 泛型算法简介
- 我们把一些常用的简单的算法抽象出来，然后用不同的迭代器去调用这些函数，这些算法被称为泛型算法
- C++标准库中的泛型算法：algorithm, numeric, ranges
- 泛型算法的实现都不复杂，但优化足够好
- 一些泛型算法与方法同名，实现功能类似，此时建议调用方法而非算法
  - std::find VS. std::map::find
  - 方法会根据对应数据结构的特性做优化，而泛型算法为了确保通用性，不会做特定优化

---

# 泛型算法分类
- 读算法：给定迭代区间，读取其中的元素并进行计算
  - accumulate/find/count
- 写算法：向迭代器中写入元素
  - 单纯写：fill/fill_n
  - 读+写：transform/copy
  - 注意：写算法一定要保证目标区间足够大
- 排序算法：改变输入序列中元素的顺序
  - sort/unique
  - 注意unique的用法，只把原序列中连在一起的相同元素合并为1个

---

# 迭代器的分类
- 泛型算法使用迭代器实现元素访问
- 常见迭代器
  - 输入迭代器：可读，可递增，典型应用是find算法
  - 输出迭代器：可写，可递增，典型应用是copy算法
  - 前向迭代器：可读写，可递增，典型应用是replace算法
  - 双向迭代器：可读写，可递增递减，典型应用是reverse算法
  - 随机访问迭代器：可读写，可增减一个整数，典型应用是sort算法
- 一些算法会根据迭代器的类别不同引入相应的优化
  - distance算法
    - 如果输入的是输入迭代器，就只能通过++的方式计算两个迭代器之间的距离
    - 如果输入的是随机访问迭代器，就可以直接用last_it - begin_it得到距离
- 特殊的迭代器
  - 插入迭代器：back_insert_iterator(back_inserter), front_insert_iterator(front_inseter), insert_iterator(inserter)
    ```
    std::vector<int> x;
    std::fill_n(x.begin(), 10, 3);  // core，因为x的内存比10小
    std::fill_n(std::back_insert_iterator<std::vector<int>>(x), 10, 3);  // 正确，因为back_insert_iterator把"="操作重载为push_back了，所以这里能正确往x中填充10个3
    std::fill_n(std::back_inserter(x), 10, 3);  // 简化版本
    for (const auto i : x) {
      std::cout << i << " ";
    }
    std::cout << std::endl;
    ```
  - 流迭代器
    - istream_iterator
      ```
      std::istringstream str("1 2 3 4 5");
      std::istream_iterator<int> x(str);
      std::cout << *x << std::endl;  // 1
      ++x;
      std::cout << *x << std::endl;  // 2

      std::istream_iterator<int> y{};  // 这一行不是类对象的定义，而是函数声明
      // std::istream_iterator<int> y;  // 这么写也可以
      for (; x != y; ++x) {
        std::cout << *x << std::endl;  // 1 2 3 4 5
      }
      std::accumulate(x, y, 0);  // 15
      ```
    - ostream_iterator
      ```
      std::vector<int> x{1, 2, 3};
      std::copy(x.rbegin(), y.rbegin(), std::ostream_iterator<int>(std::cout, " "));
      ```
  - 反向迭代器
  - 移动迭代器
    - move_iterator：功能类似于move操作，执行后原先内存的内容就没了
- 迭代器与哨兵(Sentinel)
  - 哨兵一般指标识迭代器结束的标志，比如vector.end(), 以及上面的std::istream_iterator<int> y{}，都是哨兵
  - 哨兵在ranges算法中是个非常重要的概念

---

# 并发算法(C++17/20)
- 有些算法可以通过指定执行policy来加速，下面是一些policy的举例
  - std::execution::seq：顺序执行
  - std::execution::par：并发执行
  - std::execution::par_unseq：并发非顺序执行
  - std::execution::unseq：非顺序执行
- 代码demo(注意：下面的代码可能需要在编译时加上-o3和-ltbb库的优化才能体现出加速效果)
  ```
  #include <iostream>
  #include <algorithm>
  #include <chrono>
  #include <random>
  #include <ratio>
  #include <vector>
  #include <execution>

  int main() {
    std::random_device rd;
    std::vector<double> vals(100000000);
    for (auto& d : vals) {
      d = static_cast<int>(rd());
    }
    
    for (int i = 0; i < 5; ++i) {
      using namespace std::chrono;
      std::vector<double> sorted(vals);
      const auto startTime = high_resolution_clock::now();
      std::sort(std::execution::par, sorted.begin(), sorted.end());  // 并发执行，加速
      std::sort(std::execution::unseq, sorted.begin(), sorted.end());  // 顺序执行，不加速
      const auto endTime = high_resolution_clock::now();
      std::cout << "Latency: " << duration_cast<duration<double, std::milli>>(endTime - startTime).count() << std::endl;
    }
    return 1;
  }
  ```

---

# 泛型算法的改进--ranges(C++20)
- ranges可以视为C++标准模块库的2.0版本
- 可以使用容器而非迭代器做为输入
  ```
  int main() {
    std::vector<int> x{1, 2, 3, 4, 5};
    auto it = std::find(x.begin(), x.end(), 3);
    it = std::ranges::find(x.begin(), x.end(), 3);
    it = std::ranges::find(x, 3);
    std::cout << *it << std::endl;
  }
  ```
  - 通过std::ranges::dangling避免返回无效的迭代器
- 引入映射概念，简化代码编写
  ```
  std::map<int, int> m{{2, 3}};
  auto it = std::ranges::find(m.begin(), m.end(), 3, &std::pair<const int, int>::second);  // projectionii，本质上是个指针，指向了pair的第二个元素
  std::cout << it->first << std::endl;
  ```
- 引入view，灵活组织程序逻辑
  - 通过 \| 这样的符号串联起来组成更加复杂的语句，这个在container部分有讲
- 从类型上区分迭代器与哨兵

------

# demo
- sort排序
  ```
  vector<Struct> A
  bool f1 (Struct a,Struct b) { return (a.x>b.x); }
  bool f2 (Struct a,Struct b) { return (a.x<b.x); }
  sort(A.begin(), A.end(), f1);//降序排列
  sort(A.begin(), A.end(), f2);//升序排列
  sort(A.begin(), A.end(), \[\](const struct& A, const struct& B){return A.a < B.b;})

  //cmp函数需要输入额外变量时
  bool f3(Struct a, Struct b, double c){
      if(a.y - b.y > c){
          return a.x < b.x;
      }else{
          return a.x > b.x;
      }
  }
  double c = 10;
  sort(A.begin(), A.end(), std::bind(f3,
                                      std::placeholders::_1,
                                      std::placeholders::_2,
                                      c));
  ```

- nth_element函数
  ```
  vector<int> pts;
  //只保证pts[6]是排名第6的元素,同时pts[0-5]<pts[6],pts[6-end]>pts[6]
  nth_element(pts.begin(), pts.begin()+6; pts.end())

  compare(Ponit2d* a, Ponit2d* b)
  {
    return(a->x < b->x);
  }
  vector<point2d> pts;
  nth_element(pts.begin(), pts.begin()+6; pts.end(), compare);
  ```

- lower_bound
  ```
  auto compare_s = [](const std::pair<double, double>& point, const double s) {
      return point.first < s;
  };
  vector<pair<double, double>> var;
  auto it_lower = std::lower_bound(var.begin(),
                                  var.end(), s, compare_s);
  ```

- for_each
  ```
  void add(int& lhs) {
    lhs= lhs + 1;
  }
  for_each(intVector.begin(),intVector.end(),add);

  void add(int& lhs,int rhs) {
    lhs= lhs + rhs;
  }
  for_each(intVector.begin(),intVector.end(),boost::bind(add,_1,100));
  ```
