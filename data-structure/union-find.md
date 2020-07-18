# 并查集

在计算机科学中，**并查集**是一种树型的数据结构，用于**处理一些不交集（Disjoint Sets）的合并及查询问题**。有一个**联合-查找算法**（**union-find algorithm**）定义了两个用于此数据结构的操作：

* **Find**：**确定元素属于哪一个子集**。这个**确定方法就是不断向上查找找到它的根节点**，它可以**被用来确定两个元素是否属于同一子集**。
* **Union**：**将两个子集合并成同一个集合**。

#### [lowest-common-ancestor-of-a-binary-tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（**一个节点也可以是它自己的祖先**）。

