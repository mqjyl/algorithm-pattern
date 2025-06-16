# 实现高性能local cache

## :pencil2: 1、缓存机制

### [**LRU Cache**](https://leetcode-cn.com/problems/lru-cache/)

设计和实现一个 LRU (最近最少使用) 缓存机制。它应该支持以下操作： 获取数据 get 和 写入数据 put 。

获取数据 `get(key)` - 如果关键字 (key) 存在于缓存中，则获取关键字的值（总是正数），否则返回 -1。 写入数据 `put(key, value)` - 如果关键字已经存在，则变更其数据值；如果关键字不存在，则插入该组「关键字/值」。当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间。

> 实现本题的两种操作，需要用到**一个哈希表（快速查找）和一个双向链表（维持有序性**）。在面试中，面试官一般会期望读者能够自己实现一个简单的双向链表，而不是使用语言自带的、封装好的数据结构。在 Python 语言中，有一种结合了哈希表与双向链表的数据结构 `OrderedDict`，只需要短短的几行代码就可以完成本题。在 Java 语言中，同样有类似的数据结构 `LinkedHashMap`。

### [LFU Cache](https://leetcode-cn.com/problems/lfu-cache/description/?utm_source=LCUS\&utm_medium=ip_redirect_q_uns\&utm_campaign=transfer2china)

设计并实现 最不经常使用（LFU）缓存算法数据结构。它应该支持以下操作：get 和 put。

`get(key)` - 如果键存在于缓存中，则获取键的值（总是正数），否则返回 -1。 `put(key, value)` - 如果键已存在，则变更其值；如果键不存在，请插入键值对。当缓存达到其容量时，则应该在插入新项之前，使最不经常使用的项无效。在此问题中，当存在平局（即两个或更多个键具有相同使用频率）时，应该去除最久未使用的键。 「项的使用次数」就是自插入该项以来对其调用 get 和 put 函数的次数之和。使用次数会在对应项被移除后置为 0 。

>

{% tabs %}
{% tab title="LRU" %}
```cpp
struct DLinkedList{
    int key,val;
    DLinkedList *prev;
    DLinkedList *next;
    DLinkedList() : key(0), val(0), prev(nullptr), next(nullptr){}
    DLinkedList(int _key, int _val) : key(_key), val(_val), prev(nullptr), next(nullptr){}
};

class LRUCache {
public:
    LRUCache(int capacity) : m_capacity(capacity) , m_size(0){
        head = new DLinkedList();
        tail = new DLinkedList();
        head->next = tail;
        tail->prev = head;
    }
    int get(int key) {
        if(!m_cache.count(key)){
            return -1;
        }
        DLinkedList *node = m_cache[key];
        moveToHead(node);
        return node->val;
    }
    void put(int key, int value) {
        if(m_cache.count(key)){
            DLinkedList *node = m_cache[key];
            node->val = value;
            moveToHead(node);
        }else{
            DLinkedList *node = new DLinkedList(key, value);
            m_cache[key] = node;
            addToHead(node);
            ++m_size;
            if(m_size > m_capacity){
                DLinkedList *removeNode = removeTail();
                m_cache.erase(removeNode->key);
                delete removeNode;
                --m_size;
            }
        }
    }
    // 维护双向链表
    void moveToHead(DLinkedList *_node){
        removeNode(_node);
        addToHead(_node);
    }
    DLinkedList *removeTail(){
        DLinkedList *node = tail->prev;
        removeNode(node);
        return node;
    }

    void removeNode(DLinkedList *node){
        node->prev->next = node->next;
        node->next->prev = node->prev;
    }
    void addToHead(DLinkedList *node){
        node->next = head->next;
        head->next->prev = node;
        head->next = node;
        node->prev = head;
    }

private:
    int m_capacity;
    int m_size;
    DLinkedList *head;
    DLinkedList *tail;
    std::unordered_map<int, DLinkedList*> m_cache;
};
```
{% endtab %}

{% tab title="LFU" %}
```
```
{% endtab %}
{% endtabs %}

## :pencil2: 2、实现local cache
