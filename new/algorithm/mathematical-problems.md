# 数学问题

## :pencil2: 1、阶乘问题

[**Factorial Trailing Zeroes**](https://leetcode-cn.com/problems/factorial-trailing-zeroes/)

> 找因子2和5的对数，因子 2 的数量总是比因子 5 的数量大，所以只需要找因子 5 的数量。（超时）
>
> 简化： $$cnt=\frac{n}{5}+\frac{n}{25}+\frac{n}{125}+\frac{n}{625}+\frac{n}{3125}+\cdots$$ &#x20;

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

## :pencil2: 2、公约数问题

## :pencil2: 3、幂运算

## :pencil2: 4、数字规律

### [**Next Permutation**](https://leetcode-cn.com/problems/next-permutation/)

将给定数字序列重新排列成字典序中下一个更大的排列。如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。必须原地修改，只允许使用额外常数空间。

> 思路：
>
> 1. 从后向前查找第一个相邻升序的元素对 `(i,j)`，满足 `A[i] < A[j]`。此时 `[j,end)` 必然是降序；
> 2. 在 `[j,end)` 从后向前查找第一个满足 `A[i] < A[k]` 的 k，将 A\[i] 与 A\[k] 交换；
> 3. 可以断定这时 `[j,end)` 必然是降序，逆置 `[j,end)`，使其升序；
> 4. 如果在步骤 1 找不到符合的相邻元素对，说明当前 `[begin,end)` 为一个降序顺序，则直接跳到步骤 3。

```cpp
void reversal(vector<int>& nums, int start, int stop){
    while(start < stop){
        int tmp = nums[start];
        nums[start++] = nums[stop];
        nums[stop--] = tmp;
    }
}
void nextPermutation(vector<int>& nums) {
    int len = nums.size() - 1;
    int i = len;
    while(i > 0 && nums[i] <= nums[i - 1]){  //
        i--;
    }
    if(i == 0){
        reversal(nums, 0, len);
    }else{
        i--;
        for(int j = len;j > i;j --){
            if(nums[j] > nums[i]){
                int tmp = nums[i];
                nums[i] = nums[j];
                nums[j] = tmp;
                break;
            }
        }
        reversal(nums, i + 1, len);
    }
}
```
