---
description: 总结字符串相关的算法考点和题型。
---

# 字符串

## ✏ 模式匹配

字符串 $$S=[s_1,s_2,\ldots,s_n]$$ ，模式串 $$P=[p_1,p_2,\ldots,p_m]$$ ， $$i$$ 和 $$j$$ 为两个游标。

**解决字符串的算法非常多：**

朴素算法（`Naive Algorithm`）、`Rabin-Karp` 算法、有限自动机算法（`Finite Automation`）、 `Knuth-Morris-Pratt` 算法（即 `KMP Algorithm`）、`Boyer-Moore` 算法、`Simon` 算法、`Colussi` 算法、`Galil-Giancarlo` 算法、`Apostolico-Crochemore` 算法、`Horspool` 算法和 `Sunday` 算法等。

### 🖋 1、`BF`算法

普通模式匹配算法，其实现过程没有任何技巧，就是简单粗暴地拿一个串同另一个串中的字符一一比对，得到最终结果。

时间复杂度分析，匹配成功时：

* 最好情况： $$s_1$$ 、 $$s_2$$ 、 $$\ldots$$ 、 $$s_{n-m}$$ 都与 $$p_1$$ 不匹配， $$[s_{n-m+1},\ldots,s_n]$$ 与 $$[p_1,p_2,\ldots,p_m]$$ 匹配，此时 $$i$$ 移动 $$n$$ 次， $$j$$ 移动 $$m$$ 次，复杂度 $$O(n+m)$$ 。
* 最坏情况：$$s_1$$ 、 $$s_2$$ 、 $$\ldots$$ 、 $$s_{m-1}$$ 与$$p_1$$ 、 $$p_2$$ 、 $$\ldots$$ 、 $$p_{m-1}$$ 匹配，但是 $$s_m$$ 与 $$p_m$$ 不匹配，同样，$$s_2$$ 、 $$s_3$$ 、 $$\ldots$$ 、 $$s_{m}$$ 与$$p_1$$ 、 $$p_2$$ 、 $$\ldots$$ 、 $$p_{m-1}$$ 匹配，但是$$s_{m+1}$$ 与 $$p_m$$ 不匹配，依次进行，直到$$s_{n-m+1}$$ 、 $$s_{n-m+2}$$ 、 $$\ldots$$ 、 $$s_{n}$$ 与$$p_1$$ 、 $$p_2$$ 、 $$\ldots$$ 、 $$p_{m}$$ 匹配成功，此时 $$i$$ 移动 $$m(n-m)$$ 次， $$j$$ 移动 $$m(n-m)$$ 次，复杂度 $$O(n\times m)$$ 。

### 🖋 2、`KMP`算法



### 🖋 3、题型

#### \*\*\*\*[**Implement strStr\(\)**](https://leetcode-cn.com/problems/implement-strstr/)\*\*\*\*

实现 `strStr()` 函数。给定一个 `haystack` 字符串和一个 `needle` 字符串，在 `haystack` 字符串中找出 `needle` 字符串出现的第一个位置 \(从0开始\)。如果不存在，则返回 `-1`。 对于本题而言，当 `needle` 是空字符串时我们应当返回 0 。这与C语言的 `strstr()` 以及 Java的 `indexOf()` 定义相符。



#### \*\*\*\*[**Repeated Substring Pattern**](https://leetcode-cn.com/problems/repeated-substring-pattern/)\*\*\*\*

给定一个非空的字符串，判断它是否可以由它的一个子串重复多次构成。给定的字符串只含有小写英文字母，并且长度不超过10000。

## ✏ 字符串相关算法题

### \*\*\*\*[**Add Binary**](https://leetcode-cn.com/problems/add-binary/)\*\*\*\*

给你两个二进制字符串，返回它们的和（用二进制表示）。输入为 **非空** 字符串且只包含数字 `1` 和 `0`。

* `1 <= a.length, b.length <= 10^4`
* 字符串如果不是 `"0"` ，就都不含前导零。

```text

```

### \*\*\*\*[**替换空格**](https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof/)\*\*\*\*

 请实现一个函数，把字符串 `s` 中的每个空格替换成`"%20"`。



### \*\*\*\*[**First Unique Character in a String**](https://leetcode-cn.com/problems/first-unique-character-in-a-string/)\*\*\*\*

给定一个字符串，找到它的第一个不重复的字符，并返回它的索引。如果不存在，则返回 `-1`。

