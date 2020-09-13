# 实现阻塞队列

阻塞队列是后台开发中多线程异步架构的基本数据结构，像python，java 都提供线程安全的阻塞队列，c++ 可能需要自己实现一个模板。

## 阻塞队列

阻塞队列：阻塞队列是一个在队列基础上又支持了两个附加操作的队列。

1. 支持阻塞的**插入**方法：队列满时，队列会阻塞插入元素的线程，直到队列不满。
2. 支持阻塞的**移除**方法：队列空时，获取元素的线程会等待队列变为非空。

阻塞队列常用于生产者和消费者的场景，生产者是向队列里添加元素的线程，消费者是从队列里取元素的线程。简而言之，阻塞队列是生产者用来存放元素、消费者获取元素的容器。

在阻塞队列不可用的时候，上述2个附加操作提供了四种处理方法（借鉴Java的官方实现）：

| 方法\处理方式 | 抛出异常 | 返回特殊值 | 一直阻塞 | 超时退出 |
| :--- | :--- | :--- | :--- | :--- |
| 插入方法 | add\(e\) | offer\(e\) | put\(e\) | offer\(e,time,unit\) |
| 移除方法 | remove\(\) | poll\(\) | take\(\) | poll\(time,unit\) |
| 检查方法 | element\(\) | peek\(\) | 不可用 | 不可用 |

1. `put`方法用来向队尾存入元素，如果队列满，则等待；
2. `take`方法用来从队首取元素，如果队列为空，则等待；
3. `offer`方法用来向队尾存入元素，如果队列满，则等待一定的时间，当时间期限达到时，如果还没有插入成功，则返回false；否则返回true；
4. `poll`方法用来从队首取元素，如果队列空，则等待一定的时间，当时间期限达到时，如果取到，则返回null；否则返回取得的元素；

## 优点

### 1 降低多线程开发的难度

由于阻塞队列本身是线程安全的，队列可以安全地从一个线程向另外一个线程传递数据，所以我们的生产者/消费者直接使用线程安全的队列就可以，而不需要自己去考虑更多的线程安全问题。这也就意味着，考虑锁等线程安全问题的重任从 `你` 转移到了 `队列` 上，降低了我们开发的难度和工作量。

### 2 隔离代码，实现业务代码解耦

队列还能起到一个隔离的作用。比如说我们开发一个银行转账的程序，那么生产者线程不需要关心具体的转账逻辑，只需要把转账任务，如账户和金额等信息放到（put）队列中就可以，而不需要去关心银行这个类如何实现具体的转账业务。

而作为银行这个类来讲，它会去从队列里（take）取出来将要执行的具体的任务，再去通过自己的各种方法来完成本次转账。这样就实现了具体任务与执行任务类之间的解耦，任务被放在了阻塞队列中，而负责放任务的线程是无法直接访问到我们银行具体实现转账操作的对象的，实现了隔离，提高了安全性。

## 阻塞队列的特点

阻塞队列区别于其他类型的队列的最主要的特点就是 `阻塞` 这两个字，所以下面重点介绍阻塞功能：阻塞功能使得生产者和消费者两端的能力得以平衡，当有任何一端速度过快时，阻塞队列便会把过快的速度给降下来。实现阻塞最重要的两个方法是 `take` 方法和 `put` 方法。

### 1 take 方法

take 方法的功能是获取并移除队列的头结点，通常在队列里有数据的时候是可以正常移除的。可是一旦执行 take 方法的时候，队列里无数据，则阻塞，直到队列里有数据。一旦队列里有数据了，就会立刻解除阻塞状态，并且取到数据。过程如图所示：

![](../../.gitbook/assets/image%20%2857%29.png)

### 2 put 方法

put 方法插入元素时，如果队列没有满，那就和普通的插入一样是正常的插入，但是如果队列已满，那么就无法继续插入，则阻塞，直到队列里有了空闲空间。如果后续队列有了空闲空间，比如消费者消费了一个元素，那么此时队列就会解除阻塞状态，并把需要添加的数据添加到队列中。过程如图所示：

![](../../.gitbook/assets/image%20%2853%29.png)

### 3 是否有界

阻塞队列还有一个非常重要的属性，那就是容量的大小，分为有界和无界两种。

无界队列意味着里面可以容纳非常多的元素，可以近似认为是无限容量。有界队列可以设置固定大小，如果队列满了，也不会扩容，所以一旦满了就无法再往里放数据了，put 方法会阻塞当前线程。

## C++实现

{% tabs %}
{% tab title="C++11" %}
```cpp
#include <mutex>
#include <condition_variable>
#include <deque>
#include <chrono>

template<class T>
class BlockQueue {
public:
    using size_type = typename std::deque<T>::size_type;

public:
    BlockQueue(const int cap = -1) : m_maxCapacity(cap){}
    ~BlockQueue(){}

    BlockQueue(const BlockQueue &) = delete;
    BlockQueue &operator = (const BlockQueue &) = delete;

public:
    void put(const T t);
    T take();

    bool empty() const{
        std::lock_guard<std::mutex> lock(m_mutex);
        return m_queue.empty();
    }

    bool full() const{
        if(-1 == m_maxCapacity)
            return false;
        std::lock_guard<std::mutex> lock(m_mutex);
        return m_queue.size() >= m_maxCapacity;
    }

    size_type size(){
        std::lock_guard<std::mutex> lock(m_mutex);
        return m_queue.size();
    }

public:
    bool offer(const T t);
    bool poll(T& t);
    bool offer(const T t, std::chrono::milliseconds& time);
    bool poll(T& t, std::chrono::milliseconds& time);

private:
    std::deque<T> m_queue;
    const int m_maxCapacity;
    mutable std::mutex m_mutex;
    std::condition_variable m_cond_empty;
    std::condition_variable m_cond_full;
};

template <class T>
void BlockQueue<T>::put(const T t){
    std::unique_lock<std::mutex> lock(m_mutex);
    if(m_maxCapacity != -1){
        m_cond_full.wait(lock, [this]{ return m_queue.size() < m_maxCapacity; });
    }
    m_queue.push_back(t);
    m_cond_empty.notify_all();
}

template <class T>
T BlockQueue<T>::take(){
    std::unique_lock<std::mutex> lock(m_mutex);
    // take必须判断队列为空
    m_cond_empty.wait(lock, [&](){return !m_queue.empty();});
    auto res = m_queue.front();
    m_queue.pop_front();
    m_cond_full.notify_all();
    return res;
}

template <class T>
bool BlockQueue<T>::offer(const T t){
    std::lock_guard<std::mutex> lock(m_mutex);
    if(m_maxCapacity != -1 && m_queue.size() >= m_maxCapacity){
        return false;
    }
    m_queue.push_back(t);
    m_cond_empty.notify_all();
    return true;
}

template <class T>
bool BlockQueue<T>::poll(T& t){
    std::lock_guard<std::mutex> lock(m_mutex);
    if(m_queue.empty()){
        return false;
    }
    t = m_queue.front();
    m_queue.pop_front();
    m_cond_full.notify_all();
    return true;
}

template <class T>
bool BlockQueue<T>::offer(const T t, std::chrono::milliseconds& time){
    std::lock_guard<std::mutex> lock(m_mutex);
    if(m_maxCapacity != -1){
        bool result = m_cond_full.wait(lock, time, 
                                   [&]{ return m_queue.size() < m_maxCapacity; });
        if(!result){
            return false;
        }
    }
    m_queue.push_back(t);
    m_cond_empty.notify_all();
    return true;
}

template <class T>
bool BlockQueue<T>::poll(T& t, std::chrono::milliseconds& time){
    std::lock_guard<std::mutex> lock(m_mutex);
    bool result = m_cond_empty.wait_for(lock, time, 
                                   [&] {return !m_queue.empty();});
    if(!result){
        return false;
    }
    t = m_queue.front();
    m_queue.pop_front();
    m_cond_full.notify_all();
    return true;
}

// 测试
void produce(BlockQueue<int> &q){
    const int num = 9;
    for(int i = 0; i < num; ++i){
        //q.offer(i);  // 只打印  0   1
        q.put(i);
    }
}

void consume(BlockQueue<int> &q){
    while(false == q.empty()){
        int tmp = q.take();
        cout << tmp << endl;
        std::this_thread::sleep_for(chrono::seconds(1));
    }
}

void testBlockQueue(){
    BlockQueue<int> iqueue(2);
    std::thread t1(produce, std::ref(iqueue));
    std::thread t2(consume, std::ref(iqueue));
    std::thread t3(consume, std::ref(iqueue));
    t1.join();
    t2.join();
    t3.join();
}
```
{% endtab %}

{% tab title="Linux glibc" %}
```

```
{% endtab %}
{% endtabs %}

