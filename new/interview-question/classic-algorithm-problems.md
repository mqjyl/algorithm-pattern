# 经典的算法题

## :pencil2: 1、Top-K问题

Top K 问题：从N个元素中找出最小（或最大）的K个元素

#### 解决方案

容易想到的是解决方案是，对N个元素进行排序，然后根据排序结果从中取出最小（或最大）的K个元素，但是当N的规模非常之大时，效率会非常低。而堆则可以很好解决地该问题。这里，我们以取出最小的K个元素为例，说明如何通过最大堆解决该问题（若是找出最大的K个元素，则应通过最小堆解决）：

1. 建立一个最大堆，遍历N个元素并重复下述 Step 2-3；
2. 若堆的大小未达到K时，直接将新元素放入堆底，然后通过"上浮"恢复堆的有序化；
3. 若堆的大小已经为K时，则比较根节点元素(即堆中最大元素)是否比新元素大。若是，则直接用新元素来替换该堆的根节点，并通过"下沉"恢复堆的有序化；若不是，则直接丢弃该新元素即可；
4. 当N个元素全部遍历完成后，此时堆中元素即为所要找出最小的K个元素；此时第k小元素就是堆顶元素。

如果找第k小（或大）的元素，还可以使用快排的方法（类似于二分的思想），主元位置如果大于k，则将前半部分进行partition，如果小于k，则将后一部分进行partition，刚好等于k，则停止。

## :pencil2: 2、URL去重

给定 a、b 两个文件，各存放 50 亿个 URL，每个 URL 各占 64B，内存限制是 4G。请找出 a、b 两个文件共同的 URL。

#### 解答思路

每个 URL 占 64B，那么 50 亿（ $$\approx 5GB$$ ）个 URL占用的空间大小约为 320GB。

由于内存大小只有 4G，因此，我们不可能一次性把所有 URL 加载到内存中处理。对于这种类型的题目，一般采用**分治策略** ，即：把一个文件中的 URL 按照某个特征划分为多个小文件，使得每个小文件大小不超过 4G，这样就可以把这个小文件读到内存中进行处理了。

#### **思路如下** ：

首先遍历文件 a，对遍历到的 URL 求 `hash(URL) % 1000` ，根据计算结果把遍历到的 URL 存储到 `a0, a1, a2, ..., a999`，这样每个大小约为 `300MB`。使用同样的方法遍历文件 b，把文件 b 中的 URL 分别存储到文件 `b0, b1, b2, ..., b999` 中。这样处理过后，所有可能相同的 URL 都在对应的小文件中，即 `a0` 对应 `b0, ..., a999` 对应 `b999`，不对应的小文件不可能有相同的 URL。那么接下来，我们只需要求出这 1000 对小文件中相同的 URL 就好了。

接着遍历 $$a_i$$ ( $$i\in[0,999]$$ )，把 URL 存储到一个 HashSet 集合中。然后遍历 $$b_i$$ 中每个 URL，看在 HashSet 集合中是否存在，若存在，说明这就是共同的 URL，可以把这个 URL 保存到一个单独的文件中。

#### 方法总结

1. 分而治之，进行哈希取余；
2. 对每个子文件进行 HashSet 统计。

## :pencil2: 3、海量数据排序（头条三面）

### :pen\_fountain: 3.1、1千万个整数排序

### :pen\_fountain: 3.2、有`5T`的浮点数排序

共有大小为`5T`的浮点数，五台机器，每台机器8G内存，2T磁盘，将这5T的浮点数排序。
