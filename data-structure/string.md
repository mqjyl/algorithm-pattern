---
description: 总结字符串相关的算法考点和题型。
---

# 字符串

## ✏ 模式匹配

字符串 $$S=[c_1,c_2,\ldots,c_n]$$ ，模式串 $$T=[p_1,p_2,\ldots,p_m]$$ 。

**解决字符串的算法非常多：**

朴素算法（`Naive Algorithm`）、`Rabin-Karp` 算法、有限自动机算法（`Finite Automation`）、 `Knuth-Morris-Pratt` 算法（即 `KMP Algorithm`）、`Boyer-Moore` 算法、`Simon` 算法、`Colussi` 算法、`Galil-Giancarlo` 算法、`Apostolico-Crochemore` 算法、`Horspool` 算法和 `Sunday` 算法等。

### 🖋 1、`BF`算法

普通模式匹配算法，其实现过程没有任何技巧，就是简单粗暴地拿一个串同另一个串中的字符一一比对，得到最终结果。

时间复杂度分析：

* 
### 🖋 2、`KMP`算法



## ✏ 题型

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

