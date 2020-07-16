---
description: 二进制操作相关的试题解答。
---

# 二进制

## ✏ 常见的二进制操作

#### 1、基本操作

`a=0^a=a^0`           `0=a^a`

由上面两个推导出：`a=a^b^b`

运算符`^`常用于检测出现奇数次的位：1、3、5 等。

#### 2、交换两个数

`a=a^b      b=a^b     a=a^b`

#### 3、移除最后一个1

`a=n&(n-1)`

#### 4、获取最后一个1

`diff=(n&(n-1))^n`

## ✏ 常见题目

#### 1、[single-number](https://leetcode-cn.com/problems/single-number/)

给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现了两次。找出那个只出现了一次的元素。

要求：具有线性时间复杂度，且不使用额外空间。

```cpp
int singleNumber(vector<int>& nums) {
    int result = 0;
    for(int num : nums){
        result = result ^ num;
    }
    return result;
}
```

#### 2、[single-number-ii](https://leetcode-cn.com/problems/single-number-ii/)

给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现了三次。找出那个只出现了一次的元素。

要求：具有线性时间复杂度，且不使用额外空间。

![](../.gitbook/assets/35%20%281%29.png)

> 方法一：使用 `Set` 或 `Map` 可以在 $$\mathcal{O}(N)$$ 的时间和 $$\mathcal{O}(N)$$ 的空间内解决。

```cpp
int singleNumber(vector<int>& nums) {
    long sum = 0;
    long tmpSum = 0;
    std::set<int> iset;
    for(int num : nums){
        sum += num;
        iset.insert(num);
    }
    for(std::set<int>::iterator iter = iset.begin(); iter != iset.end();iter++){
        tmpSum += *iter;
    }
    return (tmpSum * 3 - sum) / 2;
}
```

