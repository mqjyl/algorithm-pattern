# C++实现读写锁

读写锁实际是一种特殊的自旋锁，它把对共享资源的访问者划分成读者和写者，读者只对共享资源进行读访问，写者则需要对共享资源进行写操作。这种锁相对于自旋锁而言，能提高并发性，因为在多处理器系统中，它允许同时有多个读者来访问共享资源，最大可能的读者数为实际的逻辑CPU数。写者是排他性的，一个读写锁同时只能有一个写者或多个读者（与CPU数相关），但不能同时既有读者又有写者。 在读写锁保持期间也是抢占失效的。

如果读写锁当前没有读者，也没有写者，那么写者可以立刻获得读写锁，否则它必须自旋在那里，直到没有任何写者或读者。 如果读写锁没有写者，那么读者可以立即获得该读写锁，否则读者必须自旋在那里，直到写者释放该读写锁。

**互斥原则：**

* 读-读能共存
* 读-写不能共存
* 写-写不能共存

**实现方式：**

1、使用互斥锁加条件变量实现

2、使用原子操作实现

{% tabs %}
{% tab title="互斥锁" %}
```cpp
// WfirstRWLock.h
#ifndef ALGORITHM_PATTERN_CODE_WFIRSTRWLOCK_H
#define ALGORITHM_PATTERN_CODE_WFIRSTRWLOCK_H
#include <mutex>
#include <condition_variable>

class WfirstRWLock {
public:
    WfirstRWLock() = default;
    ~WfirstRWLock() = default;

    void lock_read();
    void lock_write();
    void release_read();
    void release_write();

private:
    volatile size_t m_count_read = 0;
    volatile size_t m_count_write = 0;
    volatile bool m_write_flag{ false };
    std::mutex m_mutex;
    std::condition_variable m_cond_write;
    std::condition_variable m_cond_read;
};

void testRWLock();

#endif //ALGORITHM_PATTERN_CODE_WFIRSTRWLOCK_H

// WfirstRWLock.cpp
#include "../../include/tools/WfirstRWLock.h"
#include <iostream>
#include <chrono>
#include <thread>
#include <future>
#include <ctime>
using namespace std;

void WfirstRWLock::lock_read(){
    unique_lock<mutex> lock(m_mutex);
    m_cond_read.wait(lock, [this]()->bool{return m_count_write == 0;});
    ++ m_count_read;
}

void WfirstRWLock::lock_write(){
    unique_lock<mutex> lock(m_mutex);
    m_cond_write.wait(lock, [this]()->bool{return m_count_read == 0 && !m_write_flag;});
    ++ m_count_write;
    m_write_flag = true;
}

void WfirstRWLock::release_read(){
    unique_lock<mutex> lock(m_mutex);
    -- m_count_read;
    if(m_count_read == 0 && m_count_write > 0){
        m_cond_write.notify_one();
    }
}

void WfirstRWLock::release_write(){
    unique_lock<mutex> lock(m_mutex);
    -- m_count_write;
    if(m_count_write == 0){
        m_cond_read.notify_all();
    }else{
        m_cond_write.notify_one();
    }
    m_write_flag = false;
}
// 测试代码
#if 1
static WfirstRWLock wfirstRwLock;
static int number = 1;

void getVal(promise<int> &pro){
    wfirstRwLock.lock_read();
    pro.set_value(number);
    this_thread::sleep_for(std::chrono::milliseconds(30));
    wfirstRwLock.release_read();
}

void putVal(int val){
    wfirstRwLock.lock_write();
    number = val;
    this_thread::sleep_for(std::chrono::milliseconds(100));
    wfirstRwLock.release_write();
}

void testRWLock(){
    srand((unsigned)time(NULL));
    for(int i = 0; i < 30; i++){
        int k = rand() % 10;
        if(k < 6){
            std::promise<int> resPro;
            std::future<int> resObj = resPro.get_future();
            thread r(getVal, std::ref(resPro));
            cout << resObj.get() << endl;
            r.join();
        }else{
            thread w(putVal, k + 1);
            w.join();
        }
    }
}
#endif
```
{% endtab %}

{% tab title="原子锁" %}
```

```
{% endtab %}
{% endtabs %}

