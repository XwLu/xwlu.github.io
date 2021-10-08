---
layout: wiki
title: Async & Future
categories: [C++]
description: Muti thread skills
keywords: thread, C++
---

# 前言
- 在阅读Apollo Planning代码的dp_st_graph部分的时候，遇到future这个多线程接口，特地来记录一下。

---

# 用法一：等待task执行完毕

  ```
  #include <iostream>
  #include <future>
  #include <vector>

  using namespace std;
  void task(string c){
    for (int i = 0; i < 10; i++){
      cout<<c;
    }
  }

  int main(){
    vector<future<void>> results;
    results.emplace_back(std::async(std::launch::async, task, "A"));
    results.emplace_back(std::async(std::launch::async, task, "B"));
    results.emplace_back(std::async(std::launch::async, task, "C"));
    
    for (int i = 0; i < 10; i++){
      cout<<"D";
    }

    for (auto& result : results){
      result.get();//强制阻塞main线程，直到对应的task线程执行完毕
    }
  }
  ```

---

# 用法二：同步获取异步结果
  ```
  #include <iostream>
  #include <future>
  #include <vector>


  using namespace std;

  int task(string c){
    if (c == "A"){
      std::this_thread::sleep_for(chrono::seconds(1));
      cout<<c<<endl;
      std::this_thread::sleep_for(chrono::seconds(1));
      cout<<c<<endl;
      std::this_thread::sleep_for(chrono::seconds(3));
      cout<<c<<endl;
      return 1;
    }

    else if (c == "B"){
      std::this_thread::sleep_for(chrono::seconds(1));
      cout<<c<<endl;
      std::this_thread::sleep_for(chrono::seconds(1));
      cout<<c<<endl;
      std::this_thread::sleep_for(chrono::seconds(2));
      cout<<c<<endl;
      return 2;
    }

    else{
      std::this_thread::sleep_for(chrono::seconds(1));
      cout<<c<<endl;
      std::this_thread::sleep_for(chrono::seconds(1));
      cout<<c<<endl;
      std::this_thread::sleep_for(chrono::seconds(1));
      cout<<c<<endl;
      return 3;
    }

  }

  int main(){
    vector<future<int>> results;
    results.emplace_back(std::async(std::launch::async, task, "A"));
    results.emplace_back(std::async(std::launch::async, task, "B"));
    results.emplace_back(std::async(std::launch::async, task, "C"));

    cout<<"D"<<endl;

    for (auto& result : results){
      int tmp = result.get();//异步结束的task，同步获取返回值
      cout<<"get "<<tmp<<endl;
    }
  }
  ```

- 三个task结束的时间不一样，但是程序获取task返回值是一起获取的，

