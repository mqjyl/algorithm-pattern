---
description: 学习动态规划的算法思维，总结常见的题型。
---

# 动态规划

## ✏ 动态规划

### 🖌 背景

从一道例题开始：[**Triangle**](https://leetcode-cn.com/problems/triangle/)\*\*\*\*

> 给定一个三角形，找出自顶向下的最小路径和。每一步只能移动到下一行中相邻的结点上。

> 例如，给定三角形：

> ```text
> [                        [
>      [2],                         [2]
>     [3,4],                       [3,4]
>    [6,5,7],                    [6,5,5,7]        (转换的二叉树)
>   [4,1,8,3]                [4,1,1,8,1,8,8,3]
> ]                        ]
> ```

> 自顶向下的最小路径和为 11（即，2 + 3 + 5 + 1 = 11）。

#### 方法一：DFS（遍历，分治）——暴力搜索

> 遍历和分治法都是暴力搜索，搜索的路径是

```cpp
// 分治法——借助上面转化的二叉树理解
int rMinimumTotal(vector<vector<int>>& triangle, int x, int y, int height){
    if(x == height){
        return 0;
    }
    return min(rMinimumTotal(triangle, x + 1, y, height), 
              rMinimumTotal(triangle, x + 1, y + 1, height)) + triangle[x][y];
}
int minimumTotal(vector<vector<int>>& triangle) {
    int height = triangle.size();
    return rMinimumTotal(triangle, 0, 0, height);
}
```



