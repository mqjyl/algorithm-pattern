---
description: 栈和队列的使用场景和相关算法题实现。
---

# 栈和队列

## :pencil2: 1、栈（Stack）& 队列（queue）

### :pen\_fountain: 1.1、[栈实现队列](https://leetcode-cn.com/problems/implement-queue-using-stacks/)

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

**用两个栈实现队列，两个栈的容量大小分别为P和Q，且Q>P，则队列的最大容量为** $$2P+1$$ **。**

### :pen\_fountain: 1.2、[队列实现栈](https://leetcode-cn.com/problems/implement-stack-using-queues/)

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

### :pen\_fountain: 1.3、双端队列（deque: double-ended queue）

双端队列（deque）是指允许两端都可以进行入队和出队操作的队列，deque 是 “double ended queue” 的简称。那就说明元素可以从队头出队和入队，也可以从队尾出队和入队。

### :pen\_fountain: 1.4、最小栈

在常数时间内检索到最小元素的栈。（[题目链接](https://leetcode-cn.com/problems/min-stack/)）

```cpp
class MinStack {
public:
    /** initialize your data structure here. */
    MinStack() {}
    void push(int x) {
        m_stack.push(x);
        if(min_stack.empty() || x <= min_stack.top()) // 等于的情况不能少
            min_stack.push(x);
    }
    void pop() {
        if(!m_stack.empty()){
            if(m_stack.top() == min_stack.top())
                min_stack.pop();
            m_stack.pop();
        }
    }
    int top() {
        return m_stack.top();
    }
    int getMin() {
        return min_stack.top();
    }
private:
    std::stack<int> m_stack;
    std::stack<int> min_stack;
};
```

### :pen\_fountain: 1.5、题型

#### 1、[用一个栈实现另一个栈的排序](https://www.nowcoder.com/questionTerminal/ff8cba64e7894c5582deafa54cca8ff2)

一个栈中元素的类型为整型，现在想将该栈从顶到底按从大到小的顺序排序，只许申请一个栈。除此之外，可以申请新的变量，但不能申请额外的数据结构。如何完成排序？

```cpp
void StackAndQueue::sortStack(std::stack<int>& stk){
    stack<int> t_stk;
    while(!stk.empty()){
        int k = stk.top();
        stk.pop();
        while(!t_stk.empty() && k > t_stk.top()){  // >= 不可以，全相等的特例会死循环
            stk.push(t_stk.top());
            t_stk.pop();
        }
        t_stk.push(k);
    }
    while(!t_stk.empty()){
        stk.push(t_stk.top());
        t_stk.pop();
    }
}
```

## :pencil2: 2、单调栈和单调队列

### :pen\_fountain:  2.1、单调&#x6808;**（维护凸包）**

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

### :pen\_fountain: 2.2、单调队列

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

#### :gem: **2**.2.1、特点：

1. 维护区间最值；&#x20;
2. 去除冗杂状态；&#x20;
3. 保持队列单调（最大值是单调递减序列，最小值是单调递增序列）；&#x20;
4. 最优选择在队首。

#### :gem: 2.2.2、单调队列的原理：

在处理`f[i]`时，去除冗杂、多余的状态，使得每个状态在队列中只会出现一次；同时维护一个能瞬间得出最优解的队列，减少重新访问的时间；在取得自己所需的值后，为后续的求解做好准备。

**单调队列：擅长维护区间最大 / 最小值，最小值对应着递增队列，最大值对应着递减队列；**

**单调栈：擅长维护最近大于 / 小于关系，从左侧先入栈就是维护左侧最近关系，从右侧先入栈就是维护右侧最近关系。**

### :pen\_fountain: 2.3、单调队列和单调栈的性质

1. 具有单调性
2. 容器中的元素个数永远不为空。（因为当添加一个元素时，它要么直接被添加到“尾部”，要么弹出k个比它小的数后再被添加到“尾部”）
3. 对于一个元素`i`，我们可以知道在它左边区间，第一个比它小的值，也就是`Max(v[x]|x<i&&v[x]<v[i])`在元素添加的过程中，我们会不断弹出比它小的值，最后一个弹出的值，即为所求。如果没有元素被弹出，那就无法求出，虽然这个数一定存在。\
   顺便在这里多提一句，第二个比它小的数是一定不知道的，因为不确定是否被弹出
4. 对于一个元素`i`，我们可以知道在它左边区间，第一个比它大的值，也就是`Min(v[x]|x<i&&v[x]>v[i])`在弹出比i小的所有元素后，栈顶的元素即为所求。如果栈为空，也无法求出。
5. 根据2和3，它们是元素插入时所获得的信息，我们可以推出当元素被弹出时能获得的信息：在右边区间，第一个比它大的值。
6. 我们可以统计在添加元素过程中，弹出了多少个元素。

注：这里的大于和小于并不严谨，是为了表述尽量简单。请读者自己注意大于/大于等于，小于/小于等于。根据原则：容器中等于`e`的元素也会被弹出，进行判断即可。

### :pen\_fountain: 2.4、单调队列和单调栈的应用

#### 单调队列

* 可以查询区间最值（不能维护区间k大，因为队列中很有可能没有k个元素）
* 优化DP：单调队列一般是用于优化动态规划方面问题的一种特殊数据结构，且多数情况是与定长连续子区间问题相关联

#### 单调栈

对于某个元素`i`：

* 左边区间第一个比它小的数，第一个比它大的数
* 确定这个元素是否是区间最值
* 右边区间第一个大于它的值
* 到 右边区间第一个大于它的值 的距离
* 确定以该元素为最值的最长区间

### :pen\_fountain: 2.5、题型

#### 1、[保留最大的数](https://www.nowcoder.com/questionTerminal/7f26bfeccfa44a17b6b269621304dd4a?toCommentId=653322)

给定一个十进制的正整数number，选择从里面去掉一部分数字，希望保留下来的数字组成的正整数最大。

**输入描述：**&#x8F93;入为两行内容，第一行是正整数number，`1 ≤ length(number) ≤ 50000`。第二行是希望去掉的数字数量`cnt 1 ≤ cnt < length(number)`。（字节一面）

```cpp
std::string getMaxStr(const std::string & str, int K){
    if(K == 0){
        return str;
    }else{
        int idx = 0;
        string result;
        while(K > 0 && idx < str.size()){
            if(result.empty() || result.back() >= str[idx]){
                result.push_back(str[idx++]);
            }else{
                while(K > 0  && !result.empty() && result.back() < str[idx]){
                    result.pop_back();
                    K--;
                }
                result.push_back(str[idx++]);
            }
        }
        if(K > 0)
            result = result.substr(0, result.size() - K);
        if(idx < str.size())
            result = result.append(str.substr(idx, str.size()));
        idx = 0;
        while(idx < result.size() && result[idx++] == '0');
        return result.substr(idx - 1, result.size());
    }
}
```

#### **2、**[**Trapping Rain Water**](https://leetcode-cn.com/problems/trapping-rain-water/)

#### **3、**[**Largest Rectangle in Histogram**](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)

#### 4、视野总和（摩天大楼）

#### 5、[**Next Greater Element I**](https://leetcode-cn.com/problems/next-greater-element-i/)

#### **6、**[**Next Greater Element II**](https://leetcode-cn.com/problems/next-greater-element-ii/)

#### **7、**[**Next Greater Element III**](https://leetcode-cn.com/problems/next-greater-element-iii/)

#### **8、**[发射站](https://www.luogu.com.cn/problem/P1901)

#### 9、[牛宫](https://www.luogu.com.cn/problem/P1565)

## :pencil2: **3、优先队列**

### :pen\_fountain: **3.1、优先队列 & 左式堆**

优先队列和普通队列不同的地方在于，在优先队列中，每个记录保存一个优先级值或关键字（本文使用关键字表示），任意记录正常从队尾入队，按照优先级大小或关键字的大小进行出队。最大关键字先出队，称为最大优先队列或降序优先队列；最小关键字先出队，称为最小优先队列或升序优先队列

堆是另一种与优先队列不同的数据结构，一般来说，优先队列是用堆实现的。堆是满足堆序性的树结构，分为二叉堆、d-堆、左式堆、斜堆和二项堆。

### :pen\_fountain: **3.2、题型**

#### [**Trapping Rain Water II**](https://leetcode-cn.com/problems/trapping-rain-water-ii/)
