# 散列表

哈希表（`Hash table`，也叫散列表），是根据关键码值\(`Key value`\)而直接进行访问的数据结构。也就是说，它通过把关键码值映射到表中一个位置来访问记录，以加快查找的速度。这个映射函数叫做**散列函数**，存放记录的数组叫做**散列表**。

## ✏ 基础知识

### 🖋 1、基本概念

若关键字为 ![k](https://math.jianshu.com/math?formula=k)，则其值存放在![f\(k\)](https://math.jianshu.com/math?formula=f%28k%29) 的存储位置上。由此，不需比较便可直接取得所查记录。称这个对应关系 **`f`** 为散列函数，按这个思想建立的表为散列表。

对不同的关键字![k](https://math.jianshu.com/math?formula=k)可能得到同一散列地址，即 ![k1&#x2260;k2](https://math.jianshu.com/math?formula=k1%E2%89%A0k2)，而![f\(k1\)=f\(k2\)](https://math.jianshu.com/math?formula=f%28k1%29%3Df%28k2%29) ，这种现象称为冲突（或碰撞，英语：Collision）。具有相同函数值的关键字对该散列函数来说称做**同义词**。综上所述，根据散列函数![f\(k\)](https://math.jianshu.com/math?formula=f%28k%29) 和处理冲突的方法将一组关键字映射到一个有限的连续的地址集（区间）上，并以关键字在地址集中的“像”作为记录在表中的存储位置，这种表便称为散列表，这一映射过程称为散列造表或散列，所得的存储位置称散列地址。

若对于关键字集合中的任一个关键字，经散列函数映象到地址集合中任何一个地址的概率是相等的，则称此类散列函数为均匀散列函数（`Uniform Hash function`），这就使关键字经过散列函数得到一个“随机的地址”，从而减少冲突。

### 🖋 2、构造散列函数的方法

**直接定址法**：取关键字或关键字的某个线性函数值为散列地址。即![hash\(k\)=k](https://math.jianshu.com/math?formula=hash%28k%29%3Dk) 或 ![hash\(k\)=a \cdot k+b](https://math.jianshu.com/math?formula=hash%28k%29%3Da%20%5Ccdot%20k%2Bb), 其中![a b](https://math.jianshu.com/math?formula=a%20b)为常数（这种散列函数叫做自身函数）

 **数字分析法**：假设关键字是以`r`为基的数，并且哈希表中可能出现的关键字都是事先知道的，则可取关键字的若干数位组成哈希地址。

 **平方取中法**：取关键字平方后的中间几位为哈希地址。通常在选定哈希函数时不一定能知道关键字的全部情况，取其中的哪几位也不一定合适，而一个数平方后的中间几位数和数的每一位都相关，由此使随机分布的关键字得到的哈希地址也是随机的。取的位数由表长决定。

 **折叠法**：将关键字分割成位数相同的几部分（最后一部分的位数可以不同），然后取这几部分的叠加和（舍去进位）作为哈希地址。

 **除留余数法**：取关键字被某个不大于散列表表长m的数p除后所得的余数为散列地址。即 即 ![hash\(k\)=k\,{\bmod {\,}}p](https://math.jianshu.com/math?formula=hash%28k%29%3Dk%5C%2C%7B%5Cbmod%20%7B%5C%2C%7D%7Dp) , ![p\leq m](https://math.jianshu.com/math?formula=p%5Cleq%20m)。不仅可以对关键字直接取模，也可在折叠法、平方取中法等运算之后取模。对![p](https://math.jianshu.com/math?formula=p)的选择很重要，一般取素数或![m](https://math.jianshu.com/math?formula=m)，若![p](https://math.jianshu.com/math?formula=p)选择不好，容易产生冲突。

### 🖋 3、解决冲突的方法

#### **1、拉链法**

分离链接法的做法是将同一个值的关键字保存在同一个表中。例如，对于前面：![](https://picb.zhimg.com/80/v2-1e86436d4dcfa68069e7b54e6bf509dc_720w.png)

如果再要插入元素20，则在下标为6的位置存储表头，而表的内容是13和20。这种方法的特点是需要另外分配新的单元来存储散列到同一个位置的数据。

查找的时候，除了根据计算出来的散列值找到对应位置外，还需要在链表上进行搜索。而在单链表上的查找速度是很慢的。因此可以考虑其他搜索速度较快的数据结构，就可以大大提高搜索速度。另外散列函数如果设计得好，冲突的概率其实也会很小。

#### 2、开放定址法

而开放定址法的思想是，**如果冲突发生，就选择另外一个可用的位置**。而开放定址法中也有常见的几种策略。

* 线性探测法

还是以前面的为例：![](https://pic1.zhimg.com/80/v2-eb2f4af3fa8ab2661fa088db9e6d93be_720w.png)

如果此时再要插入20，则20 % 7 = 6，但是6的位置已有元素，因此探测下一个位置（6+1）%7，在这里就是下标为0的位置。因此20的存储位置如下：![](https://pic3.zhimg.com/80/v2-5d6efab2fb66d73f36fb3dfa627bafd8_720w.png)

但这种方式的一个问题是，可能造成**一次聚集**，因为一旦冲突发生，为了处理冲突就会占用下一个位置，而如果冲突较多时，就会出现数据都**聚集在一块区域**。这样就会导致任何关键字都需要多次尝试才可能解决冲突。

* 平方探测法

顾名思义，如果说前面的探测函数是 $$F(i)=i\times 7$$ ，那么平方探测法就是$$F(i)=i^2\times 7$$。  
但是这也同样会产生**二次聚集**问题。

* 双散列

为了**避免聚集**，在探测时选择跳跃式的探测，即再使用一个散列函数，用来计算探测的位置。假设前面的散列函数为`hash1(X)`，用于探测的散列函数为`hash2(X)`，那么一种流行的选择是$$F(i)=i\times hash2(X)$$，即第一次冲突时探测`hash1(X)+hash2(X)`的位置，第二次探测`hash1(X)+2hash2(X)`的位置。

可以看到，无论是哪种开放定址法，它都要求表足够大。

#### **3、再散列**

散列表可以认为是具有固定大小的数组，那么如果插入新的数据时散列表已满，或者散列表所剩容量不多该怎么办？这个时候就需要再散列，常见做法是，建立一个是原来两倍大小的散列表，将原来表中的关键字重新散列到新表中。

## ✏ 例题

### 🖋 1、[`TwoSum` 问题](https://leetcode-cn.com/problems/two-sum/)

给定一个整数数组 `nums` 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

```cpp
vector<int> twoSum(vector<int>& nums, int target) {
    std::unordered_map<int, int> ihash;
    for(int i = 0;i < nums.size(); ++i){
        int tar = target - nums[i];
        if(ihash.count(tar) && ihash[tar] != i)
        {
            return std::vector<int>{ihash[tar], i}; // 不能反过来
        }
        ihash[nums[i]] = i;  // 必须在后面加进去，否则会被覆盖
    }
    return std::vector<int>{};
}
```

\*\*\*\*[**3Sum**](https://leetcode-cn.com/problems/3sum/)\*\*\*\*

给你一个包含 n 个整数的数组 `nums`，判断 `nums` 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有满足条件且不重复的三元组。注意：答案中不可以包含重复的三元组。

```cpp
std::vector<std::vector<int>> HashTable::threeSum(std::vector<int>& nums)
{
    sort(nums.begin(), nums.end()); // 为了去重
    int len = nums.size();
    vector<vector<int>> result;
    for(int i = 0; i < len; ++i){
        std::unordered_map<int, int> ihash;
        for(int j = i + 1;j < len; ++j){
            int tar = 0 - nums[i] - nums[j];
            if(ihash.count(tar) && ihash[tar] != j)
            {
                vector<int> temp{nums[i], nums[ihash[tar]], nums[j]};
                result.push_back(temp); //反过来
                int t = nums[j];
                while(++j < len && t == nums[j]);  // 去重
                --j;
            }
            ihash[nums[j]] = j;
        }
        int t = nums[i];
        while(++i < len && t == nums[i]); // 去重
        --i;
    }
    return result;
}
```

#### [**4Sum**](https://leetcode-cn.com/problems/4sum/)\*\*\*\*

给定一个包含 n 个整数的数组 `nums` 和一个目标值 target，判断 `nums` 中是否存在四个元素 a，b，c 和 d ，使得 a + b + c + d 的值与 target 相等？找出所有满足条件且不重复的四元组。注意：答案中不可以包含重复的四元组。

#### \*\*\*\*





