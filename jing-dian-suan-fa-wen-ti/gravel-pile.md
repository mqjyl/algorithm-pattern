# 石子堆问题

## ✏ 1、任意合并【[链接](https://www.acwing.com/problem/content/150/)】

有N堆石子，现要将石子有序的合并成一堆，规定如下：每次只能移动任意的2堆石子合并，合并花费为新合成的一堆石子的数量。求将这N堆石子合并成1堆的最小花费和最大花费。

特点：合并的是任意两堆，直接贪心即可，每次选择最小的两堆合并。本问题实际上就是哈夫曼的变形。

```cpp
int mergeStones(vector<int> &nums){
    if(nums.empty())
        return 0;
    sort(nums.begin(), nums.end());
    int idx = 0;
    int res = 0;
    while(idx < nums.size() - 1){
        nums[idx] += nums[idx + 1];
        res += nums[idx];
        nums[idx + 1] = 0;
        int tmp = nums[idx];
        int i = idx + 1;
        for(; i < nums.size() && nums[i] < tmp; ++i){
            nums[i - 1] = nums[i];
        }
        nums[i - 1] = tmp;
        while(nums[idx] == 0)
            idx++;
    }
    return res;
}
```

### [石子合并](https://www.nowcoder.com/questionTerminal/3eef8d66b0fa4f71a8498974547fe670?orderByHotValue=1&page=1&onlyReference=false)

## ✏ 2、相邻合并【[链接](https://www.luogu.com.cn/problem/P5569)】

### \*\*\*\*[**Minimum Cost to Merge Stones**](https://leetcode-cn.com/problems/minimum-cost-to-merge-stones/)\*\*\*\*

## ✏ 3、环形合并【[链接](https://www.luogu.com.cn/problem/P1880)】

## ✏ 4、分石子【[链接](https://www.nowcoder.com/questionTerminal/1ea5b4eaeff841a4918931791b000756)】

有N堆石子，第 $$i$$ 堆一共有 $$a_i$$ 个石子。可以对任意一堆石子数量大于1的石子堆进行分裂操作，分裂成两堆新的石子数量都大于等于1的石子堆。现在需要通过分裂得到m堆石子，求这m堆石子的最小值最大可以是多少？

