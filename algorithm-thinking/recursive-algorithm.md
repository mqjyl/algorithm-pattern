---
description: 分析递归算法的设计思想和应用场景，对常见的问题给出分析和代码实现。
---

# 递归算法

递归（Recursion）在计算机科学中是指一种通过重复将问题分解为同类的子问题而解决问题的方法，其核心思想是分治策略。

> 递归与循环的区别于联系
>
> 相同点： 
>
> 1. 都是通过控制一个变量的边界（或者多个），来改变多个变量为了得到所需要的值，而反复而执行的；
> 2. 都是按照预先设计好的推断实现某一个值求取（请注意，在这里循环要更注重过程，而递归偏结果一点）。
>
> 不同点： 
>
> 递归通常是逆向思维居多，“递” 和 “归” 不一定容易发现；而循环从开始条件到结束条件，包括中间循环变量，都需要表达出来。

> 简单的来说就是：**用循环能实现的，递归一般可以实现，但是能用递归实现的，循环不一定能实现，但往往借助一个栈可以实现，因为递归调用就是在内存中压栈。**

### [swap-nodes-in-pairs](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

> 给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。 **不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

```text

```
