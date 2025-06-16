---
description: 总结 SGI STL 中的 deque、stack 和 queue 三个容器的用法。
---

# deque/priority\_queue

## :pencil2: 1、Deque



## :pencil2: 2、Stack & Queue



## :pencil2: 3、priority\_queue

优先队列的特点则遵守以下两条规则：

**- 最大优先队列：无论入队的顺序，当前最大的元素先出列。**\
&#xNAN;**- 最小优先队列：无论入队的顺序，当前最小的元素先出列。**

### :pen\_fountain: **3.1、操作**

#### **1、**&#x5165;队操作&#x20;

优先队列本质上就是用二叉堆来实现的，每次插入一个数据都是插入到数据数组的最后一个位置，然后再做上浮操作，如果插入的数是数组中最大数，自然会上浮到堆顶。如“图1 入队操作”所示：

![](<../../.gitbook/assets/image (61).png>)

#### 2、出队操作

出队操作就是返回堆顶最小堆的数据之后用数组最后的数插入到堆顶，之后再做下沉操作。如“图2 出队操作”所示：

![](<../../.gitbook/assets/image (34).png>)

时间复杂度：因为二叉堆上浮和下沉的时间复杂度都为 $$log2(n)$$ ，所以入队和出队的时间复杂度也是 $$log2(n)$$ 。

### :pen\_fountain: 3.2、题型

#### [**Merge k Sorted Lists**](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

{% tabs %}
{% tab title="分治归并" %}
```
```
{% endtab %}

{% tab title="优先队列" %}
```
```
{% endtab %}
{% endtabs %}
