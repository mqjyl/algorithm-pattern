---
description: 栈和队列的使用场景和相关算法题实现。
---

# 栈和队列

## ✏ 1、栈（Stack）& 队列（queue）

### 🖋 1.1、[栈实现队列](https://leetcode-cn.com/problems/implement-queue-using-stacks/)

```cpp
class MyQueue {
public:
    /** Initialize your data structure here. */
    MyQueue() {
        m_stack1 = new std::stack<int>();
        m_stack2 = new std::stack<int>();
    }
    /** Push element x to the back of queue. */
    void push(int x) {
        m_stack1->push(x);
    }
    /** Removes the element from in front of queue and returns that element. */
    int pop() {
        if(m_stack2->empty()){
            while(!m_stack1->empty()){
                m_stack2->push(m_stack1->top());
                m_stack1->pop();
            }
        }
        int re = m_stack2->top();
        m_stack2->pop();
        return re;
    }
    /** Get the front element. */
    int peek() {
        if(m_stack2->empty()){
            while(!m_stack1->empty()){
                m_stack2->push(m_stack1->top());
                m_stack1->pop();
            }
        }
        return m_stack2->top();
    }
    /** Returns whether the queue is empty. */
    bool empty() {
        return m_stack1->empty() && m_stack2->empty();
    }
private:
    std::stack<int> *m_stack1;
    std::stack<int> *m_stack2;
};
```

### 🖋 1.2、[队列实现栈](https://leetcode-cn.com/problems/implement-stack-using-queues/)

{% tabs %}
{% tab title="双队列" %}
```cpp
class MyStack {
public:
    /** Initialize your data structure here. */
    MyStack() {
        m_queue1 = new std::queue<int>();
        m_queue2 = new std::queue<int>();
    }
    /** Push element x onto stack. */
    void push(int x) {
        m_queue1->push(x);
    }
    /** Removes the element on top of the stack and returns that element. */
    int pop() {
        while(m_queue1->size() > 1){
            m_queue2->push(m_queue1->front());
            m_queue1->pop();
        }
        int re = m_queue1->front();
        m_queue1->pop();
        std::queue<int> *tmp = m_queue1;
        m_queue1 = m_queue2;
        m_queue2 = tmp;
        return re;
    }
    /** Get the top element. */
    int top() {
        while(m_queue1->size() > 1){
            m_queue2->push(m_queue1->front());
            m_queue1->pop();
        }
        int re = m_queue1->front();
        m_queue1->pop();
        m_queue2->push(re);
        std::queue<int> *tmp = m_queue1;
        m_queue1 = m_queue2;
        m_queue2 = tmp;
        return re;
    }
    /** Returns whether the stack is empty. */
    bool empty() {
        return m_queue1->empty();
    }
private:
    std::queue<int> *m_queue1;
    std::queue<int> *m_queue2;
};
```
{% endtab %}

{% tab title="单队列" %}
```cpp
class MyStack {
public:
    /** Initialize your data structure here. */
    MyStack() {
        m_queue = new std::queue<int>();
    }
    /** Push element x onto stack. */
    void push(int x) {
        m_queue->push(x);
    }
    /** Removes the element on top of the stack and returns that element. */
    int pop() {
        int size = m_queue->size();
        while(size > 1){
            m_queue->push(m_queue->front());
            m_queue->pop();
            size--;
        }
        int re = m_queue->front();
        m_queue->pop();
        return re;
    }
    /** Get the top element. */
    int top() {
        int size = m_queue->size();
        while(size > 1){
            m_queue->push(m_queue->front());
            m_queue->pop();
            size--;
        }
        int re = m_queue->front();
        m_queue->pop();
        m_queue->push(re);
        return re;
    }
    /** Returns whether the stack is empty. */
    bool empty() {
        return m_queue->empty();
    }
private:
    std::queue<int> *m_queue;
};
```
{% endtab %}
{% endtabs %}

### 🖋 1.3、双端队列（deque: double-ended queue）

双端队列（deque）是指允许两端都可以进行入队和出队操作的队列，deque 是 “double ended queue” 的简称。那就说明元素可以从队头出队和入队，也可以从队尾出队和入队。

### 🖋 1.4、题型



## ✏ 2、单调栈和单调队列

### 🖋  2.1、单调栈

单调栈中存放的数据是有序的，所以单调栈也分为**单调递增栈**和**单调递减栈**

* 单调递增栈：栈中数据**出栈**的序列为单调递增序列
* 单调递减栈：栈中数据**出栈**的序列为单调递减序列

`PS`：这里一定要注意所说的递增递减指的是出栈的顺序，而不是在栈中数据的顺序。

```cpp
stack<int> istack;
//此处一般需要给数组最后添加结束标志符，具体下面例题会有详细讲解
for (遍历这个数组)
{
		if (栈空 || 栈顶元素大于等于当前比较元素)
		{
				入栈;
		}
		else
		{
				while (栈不为空 && 栈顶元素小于当前元素)
				{
							栈顶元素出栈;
							更新结果;
				}
				当前数据入栈;
		}
}
```

### 🖋 2.2、单调队列

单调队列中存放的数据也是有序的，所以单调队列也分为**单调递增队列**和**单调递减队列**

* 单调递增队列：队列中数据**出队**的序列为单调递增序列
* 单调递减队列：队列中数据**出队**的序列为单调递减序列

`PS`：这里一定要注意所说的递增递减指的是出队（队头出）的顺序，和数据在队列中的顺序是一样的。

单调队列只能从队尾插入，但可以从两头删除。可以用双端队列实现。

```cpp
// 假设有 n 个元素的序列，要求解的是长度为 k 的区间的最大值
// 队列que是STL的双向队列deque
// 队列存放的是元素在序列中的序号
deque<int> iqueue;// 双向队列
for(遍历这个数组)
{
    while(!iqueue.empty() && a[iqueue.back()]<a[i])
    {
        iqueue.pop_back(); // 去尾操作
    }
    iqueue.push_back(i); // 新元素(的序号) 入队列
    if(i >= k) // 这个很明显
    {
        while(!iqueue.empty() && iqueue.front() < i-k+1)
        {
            iqueue.pop_front(); // 删头操作 
        }
        cout << a[iqueue.front()] << " ";// 取解操作
    }
}
```

#### 💎 **2**.2.1、特点：

1. 维护区间最值； 
2. 去除冗杂状态； 
3. 保持队列单调（最大值是单调递减序列，最小值是单调递增序列）； 
4. 最优选择在队首。

#### 💎 2.2.2、单调队列的原理：

在处理`f[i]`时，去除冗杂、多余的状态，使得每个状态在队列中只会出现一次；同时维护一个能瞬间得出最优解的队列，减少重新访问的时间；在取得自己所需的值后，为后续的求解做好准备。

**单调队列：擅长维护区间最大 / 最小值，最小值对应着递增队列，最大值对应着递减队列；**

**单调栈：擅长维护最近大于 / 小于关系，从左侧先入栈就是维护左侧最近关系，从右侧先入栈就是维护右侧最近关系。**

### 🖋 2.3、题型

