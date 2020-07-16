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

> 方法一：使用 `Set` 或 `Map` 可以在 $$\mathcal{O}(N)$$ 的时间和 $$\mathcal{O}(N)$$ 的空间内解决。
>
> 1、将输入数组存储到 `Set`，然后使用 `Set` 中数字和的三倍与数组之和比较。
>
> $$3 \times (a + b + c) - (a + a + a + b + b + b + c) = 2 c$$ 
>
> 2、使用`map`，遍历输入数组，统计每个数字出现的次数，最后返回出现次数为 1 的数字。

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

> 方法二：使用位运算，可以在$$\mathcal{O}(N)$$ 的时间和 $$\mathcal{O}(1)$$ 的空间内解决。
>
> 1、考虑数字的二进制形式，对于出现三次的数字，各 二进制位 出现的次数都是 3 的倍数。 因此，统计所有数字的各二进制位中 1 的出现次数，并对 3 求余，结果则为只出现一次的数字。数组 `counts` 长度恒为 `32` ，占用常数大小的额外空间。

![](../.gitbook/assets/51%20%281%29.png)

> 2、有限状态自动机+位运算

