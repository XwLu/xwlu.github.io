---
layout: wiki
title: Container 
categories: [C++]
description: Container for C++
keywords: container, C++
---

## 容器
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
- \#include<functional>
- 排序准则
```
int a[]={3,1,4,2,5};
int len=sizeof(a)/sizeof(int);//这里切记要除以sizeof(int)
sort(a ,a + len, greater<int>());//内置类型的由大到小排序
sort(a ,a + len, less<int>());//内置类型的由小到大排序
```

### std::set & std::multiset
- \#include<set>
- \#include<multiset>
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

### std::tuple
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