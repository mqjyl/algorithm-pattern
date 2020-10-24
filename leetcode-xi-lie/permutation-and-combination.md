# 排列 & 组合

## ✏ 1、排列

### 1.1、全排列

### 1.2、全排列（有重复元素）

### 1.3、[第k个排列](https://leetcode-cn.com/problems/permutation-sequence/)

给出集合 `[1,2,3,…,n]`，其所有元素共有 `n!` 种排列。按从小到大的顺序列出所有排列情况，返回第 k 个排列。

说明：给定 n 的范围是 `[1, 9]`。 给定 k 的范围是`[1, n!]`。

> 思路：确定了 $$a_1$$ 后，这些排列从编号 $$(a_1 - 1)\cdot(n - 1)!$$ 开始，到 $$a_1\cdot(n - 1)!$$ 结束，总计 $$(n - 1)!$$ 个，因此第 k 个排列实际上就对应着这其中的第 $$k \prime =(k−1)mod(n−1)!+1$$ 个排列。这样一来，我们就把原问题转化成了一个完全相同但规模减少 1 的子问题：求 $$[1, n] \backslash a_1$$ 这 $$n - 1$$ 个元素组成的排列中，第 $$k\prime$$ 小的排列。

```cpp
string getPermutation(int n, int k) {
    vector<int> factorials = {1, 1, 2, 6, 24, 120, 720, 5040, 40320};
    vector<int> nums(n, 0);
    iota(std::begin(nums), std::end(nums), 1);
    string ret;
    k --; // 从第 0 个开始
    for(int i = n - 1; i >= 0; i --){
        auto it = nums.begin() + k / factorials[i];
        ret += ('0' + *it);
        nums.erase(it);
        k %= factorials[i];
    }
    return ret;
}
```

> 对于给定的排列 $$a_1, a_2, \cdots, a_n$$，你能求出 k 吗？
>
> 解答： $$k = \left( \sum_{i=1}^n \textit{order}(a_i) \cdot (n-i)! \right) + 1$$ ，其中 $$\text{order}(a_i)$$ 表示 __$$a_{i+1}, \cdots, a_n$$ 中小于 $$a_i$$ 的元素个数。

## ✏ 2、组合

## ✏ 3、子集

\*\*\*\*

