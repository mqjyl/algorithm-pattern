# 实现环形队列

### 线性队列原理图：

假设是长度为5的数组，初始状态，空队列如所示，front与 rear指针均指向下标为0的位置。然后入队a1、a2、a3、a4， front指针依然指向下标为0位置，而rear指针指向下标为4的位置。

![](../../.gitbook/assets/image%20%2858%29.png)

出队a1、a2，则front指针指向下标为2的位置，rear不变，如下图所示，再入队a5，此时front指针不变，rear指针移动到数组之外 ，造成假溢出的现象：

![](../../.gitbook/assets/image%20%2859%29.png)

为了解决这个问题，引入了**循环队列**的概念．

### 循环队列原理图：

![](../../.gitbook/assets/image%20%2855%29.png)

可以看到当循环队列属于上图的`d1`情况时，是无法判断当前状态是队空还是队满。为了达到判断队列状态的目的，可以通过牺牲一个存储空间来实现。如上图`d2`所示：

* **队头指针在队尾指针的下一位置时，队满。** `Q.front == (Q.rear + 1) % MAXSIZE` 因为队头指针可能又重新从0位置开始，而此时队尾指针是`MAXSIZE - 1`，所以需要求余。
* **当队头和队尾指针在同一位置时，队空。** `Q.front == Q.rear`。

还有一种方式是**附加一个标志位tag** ：

* 当head赶上tail，队列空，则令tag=0；
* 当tail赶上head，队列满，则令tag=1。

### C++实现

```cpp
const int maxn = 100;

template <class T>
class CircularQueue {
public:
    CircularQueue();
    CircularQueue(const int len);
    ~CircularQueue();

public:
    bool empty();
    bool full();
    void push(T x);
    void pop();
    T front();
    int size();

public:
    bool resize();

private:
    T *m_data;
    int m_front;
    int m_rear;
    int m_length;
};

template <class T>
CircularQueue<T>::CircularQueue(){
    m_front = 0;
    m_rear = 0;
    m_data = new T[maxn];
    m_length = maxn;
}

template <class T>
CircularQueue<T>::CircularQueue(const int len){
    m_front = 0;
    m_rear = 0;
    m_data = new T[len];
    m_length = len;
}

template <class T>
CircularQueue<T>::~CircularQueue(){
    delete[] m_data;
    m_data = nullptr;
}

template <class T>
bool CircularQueue<T>::empty(){
    return m_front == m_rear;
}

template <class T>
bool CircularQueue<T>::full(){
    return m_front == (m_rear + 1) % m_length;
}

template <class T>
void CircularQueue<T>::push(T x){
    if(full()){
        resize();
    }
    m_data[m_rear] = x;
    m_rear = (m_rear + 1) % m_length;
}

template <class T>
void CircularQueue<T>::pop(){
    if(empty()){
        return;
    }
    m_front = (m_front + 1) % m_length;
}

template <class T>
T CircularQueue<T>::front(){
    if(!empty()){
        return m_data[front()];
    }
}

template <class T>
int CircularQueue<T>::size(){
    return (m_rear - m_front + m_length) % m_length;
}

template <class T>
bool CircularQueue<T>::resize(){
    T *tmp = new T[m_length * 1.5];
    int count = 0;
    for(int i = m_front; i < m_rear; i = (i + 1) % m_length){
        tmp[count++] = m_data[i];
    }
    m_front = 0;
    m_rear = count;
    m_length *= 2;
    delete[] m_data;
    m_data = tmp;
}
```



