# 并查集

## ✏ 并查集（树）

在计算机科学中，**并查集**是一种树型的数据结构，用于**处理一些不交集（Disjoint Sets）的合并及查询问题**。有一个**联合-查找算法**（**union-find algorithm**）定义了两个用于此数据结构的操作：

* `Find`：确定元素属于哪一个子集。这个确定方法就是不断向上查找找到它的根节点，它可以被用来确定两个元素是否属于同一子集。
* `Union`：将两个子集合并成同一个集合。

由于支持这两种操作，一个不相交集也常被称为联合-查找数据结构（`union-find data structure`）或合并-查找集合（`merge-find set`）。其他的重要方法：`MakeSet`，用于建立单元素集合。有了这些方法，许多经典的划分问题可以被解决。

为了更加精确的定义这些方法，需要定义如何表示集合。一种常用的策略是为每个集合选定一个固定的元素，称为代表，以表示整个集合。接着，`Find(x)` 返回 `x` 所属集合的代表，而 `Union` 使用两个集合的代表作为参数。在并查集树中，**每个集合的代表即是集合的根节点**。

* “查找”根据其父节点的引用向根行进直到到底树根。
* “联合”将两棵树合并到一起，这通过将一棵树的根连接到另一棵树的根。

**由于创建的树可能会严重不平衡，因此需要优化树结构：**

#### 🖌 **1、按秩合并**

**总是将更小的树连接至更大的树上**。因为影响运行时间的是树的深度，更小的树添加到更深的树的根上将不会增加秩除非它们的秩相同。在这个算法中，**术语“秩”替代了“深度”**，因为同时应用了路径压缩时秩将不会与高度相同。**单元素的树的秩定义为0，当两棵秩同为`r`的树联合时，它们的秩`r+1`**。只使用这个方法将使最坏的运行时间提高至每个`MakeSet`、`Union`或`Find`操作都为 $$O(log n)$$ 。

#### 🖌 2、路径压缩

 “路径压缩”，是一种在执行“查找”时扁平化树结构的方法。关键在于**在路径上的每个节点都可以直接连接到根上，**他们都有同样的表示方法。为了达到这样的效果，`Find`递归地经过树，改变每一个节点的引用到根节点。得到的树将更加扁平，为以后直接或者间接引用节点的操作加速。

这两种方法的优势互补，同时使用二者的程序每个操作的平均时间仅为![O\(\alpha \(n\)\)](https://wikimedia.org/api/rest_v1/media/math/render/svg/0f2f9c5bf5571b12dd0907f0a1ef917e1c082201)，![\alpha \(n\)](https://wikimedia.org/api/rest_v1/media/math/render/svg/74c1642d9e2f86b9e5c86c0f18ee5377507da827)是![n=f\(x\)=A\(x,x\)](https://wikimedia.org/api/rest_v1/media/math/render/svg/5bcc6ab1771bb643699c39ff45998c876e788261)的反函数，其中![A](https://wikimedia.org/api/rest_v1/media/math/render/svg/7daff47fa58cdfd29dc333def748ff5fa4c923e3)是急速增加的阿克曼函数。因为![\alpha \(n\)](https://wikimedia.org/api/rest_v1/media/math/render/svg/74c1642d9e2f86b9e5c86c0f18ee5377507da827)是其的反函数，故![\alpha \(n\)](https://wikimedia.org/api/rest_v1/media/math/render/svg/74c1642d9e2f86b9e5c86c0f18ee5377507da827)在![n](https://wikimedia.org/api/rest_v1/media/math/render/svg/a601995d55609f2d9f5e233e36fbe9ea26011b3b)十分巨大时还是小于5。因此，平均运行时间是一个极小的常数。实际上，这是渐近最优算法：Fredman和Saks在1989年解释了![\Omega \(\alpha \(n\)\)](https://wikimedia.org/api/rest_v1/media/math/render/svg/9bfdfd41667be083aff4b7c292a9f35884b654b8)的平均时间内可以获得任何并查集。

#### [lowest-common-ancestor-of-a-binary-tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（**一个节点也可以是它自己的祖先**）。

