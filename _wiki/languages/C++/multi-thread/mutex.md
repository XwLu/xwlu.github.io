---
layout: wiki
title: Mutex 
categories: [wiki]
description: Mutex for C++
keywords: mutex, C++
---

## 锁
- C++提供了四种锁：互斥锁、条件变量、自旋锁和读写锁
- 当一个线程中的函数获取了锁，在锁没有消失(超出作用域)前，另一个线程获取该锁的操作(函数)将无法进行。

------

### 读写锁
#### 通常读写锁需要完成以下功能：
- 当 data 被线程A读取时，其他线程仍可以进行读取却不能写入
- 当 data 被线程A写入时，其他线程既不能读取也不能写入

#### 对应于功能1,2我们可以这样来描述：
- 当线程A获得共享锁时，其他线程仍可以获得共享锁但不能获得独占锁
- 当线程A获得独占锁时，其他线程既不能获得共享锁也不能获得独占锁
```
    void demo()
    {
        typedef boost::shared_lock<boost::shared_mutex> read_lock;
        typedef boost::unique_lock<boost::shared_mutex> write_lock;
        boost::shared_mutex read_write_mutex;
        int32_t data = 1;
        //线程A,读data
        {
            read_lock rlock(read_write_mutex);
            std::cout << data << std:; endl;
        }
        //线程B,读data
        {
        	read_lock rlock(read_write_mutex);
        	std::cout << data << std:; endl;
        }
        //线程C,写data
        {
        	write_lock rlock(read_write_mutex);
        	data = 2;
        }
    }
```
### 注意：上面的锁的作用域就是锁所在的{}

------

### 条件变量
- 相关的类包括 **std::condition_variable**和 **std::condition_variable_any**，还有枚举类型**std::cv_status**。另外还包括函数 **std::notify_all_at_thread_exit()**，下面分别介绍一下以上几种类型。

#### **std::condition_variable**
- 当 **std::condition_variable**对象的某个wait 函数被调用的时候，它使用 **std::unique_lock**(通过 std::mutex) 来锁住当前线程。当前线程会一直被阻塞，直到另外一个线程在相同的 **std::condition_variable** 对象上调用了 **notification** 函数来唤醒当前线程。
- **std::condition_variable** 对象通常使用 **std::unique_lock** 来等待，如果需要使用另外的 **lockable** 类型，可以使用**std::condition_variable_any**类

```
    #include <thread>
    #include <mutex>
    #include <condition_variable>
    void demo()
    {
      std::mutex mtx; // 全局互斥锁.
      std::condition_variable cv; // 全局条件变量.
      bool ready = false; // 全局标志位.

      //线程A
      {
        std::unique_lock <std::mutex> lck(mtx);
        while(!ready)
          cv.wait(lck);// 当前线程被阻塞, 当全局标志位变为 true 之后,线程被唤醒, 继续往下执行打印线程编号id.
        std::cout << "thread " << id << '\n';
      }

      //线程B
      {
        std::unique_lock <std::mutex> lck(mtx);
        ready = true;
        cv.notify_all(); // 唤醒所有线程.
      }
    }
```
