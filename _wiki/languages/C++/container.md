---
layout: wiki
title: Container 
categories: [C++]
description: Container for C++
keywords: container, C++
---

## 容器
### 容器分类
- 序列容器：对象有序排列，使用整数值进行索引
- 关联容器：对像顺序不重要，利用键进行索引
- 适配器：调整原有容器行为，使其对外展现出新的类型、接口或返回新的元素
- 生成器：构造元素序列

### 迭代器
- 获取迭代器
  - begin, end
  - cbegin, cend  (const)
  - rbegin, rend
  - crbegin, crend
- 迭代器分类
  - Input Iterator
  - Output Iterator
  - Forward Iterator = Input Iterator + Output Iterator，只能往前挪
  - Bidirection Iterator，可以双向挪动
  - Random Access Iterator，支持iter+N
- 支持迭代器的容器统称为range

### 序列容器
- array:元素个数固定的序列容器，不支持添加和删除
  - 当容器中的元素是连续存储的时候，容器都会有一个data()接口，返回指向第一个元素的指针
  - swap的实现是元素复制，效率很低
- vector:元素连续存储的序列容器
  - vector也有data()接口
  - swap是指针交换，效率很高
  - emplace_back()相比push_back()少了一次对象的拷贝或者移动，因此当对象是string或者自定义结构数据时，用emplace_back效率更高
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
  - 短字符串优化，short string optimization: SSO

### 关联容器
- 底层实现分类
  - set/map/multiset/multimap
    - 底层用红黑树实现
  - unordered_xxx
    - 底层用hash表实现
- set
  - 元素需要支持使用<比较大小，如果是自定义的元素，需要定义比较函数并在初始化的时候传入std::set<MyType, Cmp>
  
  ```
  struct MyType{
    int x;
  };

  bool MyCmp(const MyType& lhs, const MyType& rhs) {
    return lhs.x < rhs.x;
  }
  
  std::set<MyType, decltype(&MyCmp)> s({MyType(3), MyType(5)}, MyCmp);
  ```
  
  - 插入insert/emplace/emplace_hint
    - emplace_hint可以给出一些插入的提示，从而加速插入的速度，但是如果hint给错了，反而增加耗时
  
  ```
  std::set<MyType, decltype(&MyCmp)> s({MyType{3}, MyType{5}, MyCmp);
  s.insert(MyType{100});
  s.emplace(100);  // 两者等价，推荐使用emplace，减少拷贝和移动
  ```
  
  - 提供了extract来修改元素(C++17)，但是操作很复杂。
  - set的迭代器是只读的，不能用于修改元素。 

### 以下是一些选择容器的基本原则：
- #### 除法你有很好的理由选择其他容器，否则应该使用vector；
- #### 如果你的程序有很多小的元素，且空间的额外开销很重要，则不要使用list或forward_list；
- #### 如果程序要求随机访问元素，应使用vector或deque；
- #### 如果程序要求在容器的中间插入或删除元素，应使用list或forward_list；

### 容器介绍
- list:
  - 头尾都可以操作
  - 插入删除很快,随机访问很慢
- deque:
  - 双端队列，可以高效的在头尾两端插入和删除元素,
  - 双端队列不保证内部的元素是按连续的存储空间存储的，因此，不允许对指针直接做偏移操作来直接访问元素
- set:
  - set的特性是，所有元素都会根据元素的键值自动排序，set的元素不像map那样可以同时拥有实值(value)和键值(key),set元素的键值就是实值，实值就是键值。
  - set不允许两个元素有相同的键值。
- map:
  - 两种赋值方式，其中数组赋值方式遇到key值相同的情况会直接覆盖，insert方式会丢弃之前的数据。
- unordered_map:
  - 关联性：元素根据键来引用，而不是根据索引来引用。
  - 无序性：元素不会根据其键值或映射值按任何特定顺序排序，而是根据其哈希值组织到桶中，以允许通过键值直接快速访问各个元素（常量的平均时间复杂度）。
  - 唯一性：std::unorederd_map中的元素的键是唯一的。

### 容器操作
#### vector
- erase():删除该成员的同时返回指向下一个成员的指针;
```
    vector<int> A;
    vector<int>::iterator it=A.begin()    
    while(it!=A.end()){
        if(*it == 0){
            it = A.erase(it)
            continue;
        }
    }
```

### std::greater & std::loss
- \#include\<functional\>
- 排序准则
```
int a[]={3,1,4,2,5};
int len=sizeof(a)/sizeof(int);//这里切记要除以sizeof(int)
sort(a ,a + len, greater<int>());//内置类型的由大到小排序
sort(a ,a + len, less<int>());//内置类型的由小到大排序
```

### std::set & std::multiset
- \#include\<set\>
- \#include\<multiset\>
- set和multiset都会根据特定的排序准则，自动将元素排序，两者不同在于multiset允许元素重复，而set不允许元素重复。
- set和multiset的排序准备默认为由小到大，也可以自定义排序准则：
```
std::set<float,std::greater<float>> var;
var.insert(1.0);
var.insert(3.0);
var.insert(2.0);
for (auto&it : var) {
	cout << it << " ";
}
//ouput: 3.0 2.0 1.0
```

### std::tuple & std::tie
- #### tuple
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
- #### tie
  - tuple将几个不同类型的变量打包为一个对象，tie则负责将tuple类型的对象解构为几个变量
  ```
  tuple<int,double,string> t3 = {1, 2.0, "3"};
  int i; double d; string s;
  tie(i, d, s) = t3;
  tie(i, d, s) = {1, 2.0, "3"};//这一行会报错，因为tie只能解构tuple
  ```

### std::array
- #### 定义
- 定义时必须指定array的大小，因为大小是模板参数之一，不可忽略
- 定义时不能使用变量指定大小
- 可通过array构造新的array，可以使用{}构造
- 不可使用数组构造
```
array<int, 5> myarray = {1,2,3,4,5};
array<int,5> otherarray = myarray;
```
- #### 访问
- 可通过下标运算符[]对元素进行操作，还可以通过at/front/back进行操作
```
for (int i = 0; i < 5; i++){
  cout << setw(10) << << myarray.at(i) << endl;
}
```
- 可以通过正向和反向迭代器对元素进行遍历
```
for (auto it = myarray.begin(); it != myarray.end();++it){
  cout << *it << endl;
}
```

---

#### 注意
- 尽量使用at方法来访问元素，因为运算符\[\]不会对索引值进行检查，像myarray\[-1\]是不会报错的。使用at()，将在运行期间捕获非法索引的，默认将程序中断。
