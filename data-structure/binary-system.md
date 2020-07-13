---
description: 二进制操作相关的试题解答。
---

# 二进制

## ✏ 常见的二进制操作

#### 1、基本操作

`a=0^a=a^0`           `0=a^a`

由上面两个推导出：`a=a^b^b`

#### 2、交换两个数

`a=a^b      b=a^b     a=a^b`

#### 3、移除最后一个1

`a=n&(n-1)`

#### 4、获取最后一个1

`diff=(n&(n-1))^n`

## ✏ 常见题目

#### 1、[single-number](https://leetcode-cn.com/problems/single-number/)

给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

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

