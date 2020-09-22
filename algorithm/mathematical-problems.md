# 数学问题

## ✏ 1、阶乘问题

\*\*\*\*[**Factorial Trailing Zeroes**](https://leetcode-cn.com/problems/factorial-trailing-zeroes/)\*\*\*\*

> 找因子2和5的对数，因子 2 的数量总是比因子 5 的数量大，所以只需要找因子 5 的数量。（超时）
>
> 简化： $$cnt=\frac{n}{5}+\frac{n}{25}+\frac{n}{125}+\frac{n}{625}+\frac{n}{3125}+\cdots$$

```cpp
int trailingZeroes(int n) {
    int count = 0;
    while(n){
        n /= 5;
        count += n;
    }
    return count;
}
```

大数阶乘（腾讯面试题）

## ✏ 2、公约数问题

## ✏ 3、幂运算

