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

> 遍历和分治法都是暴力搜索，搜索的路径是三角形转换而来二叉树的谦虚遍历。

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

**方法二：记忆化搜索——优化 DFS，缓存已经被计算的值（空间换时间），本质上是动态规划**

动态规划就是把大问题变成小问题，并解决了小问题重复计算的方法称为动态规划

> 动态规划和 DFS 区别
>
> * 二叉树 子问题是没有交集，所以大部分二叉树都用递归或者分治法，即 DFS，就可以解决
> * 像 triangle 这种是有重复走的情况，**子问题是有交集**，所以可以用动态规划来解决
>
> **可以用二维数组，可以借助一维数组（长度为三角形的行数），也可以原地操作**

```cpp
// 自底向上
int minimumTotal(vector<vector<int>>& triangle) {
    vector<vector<int>> hash;
    vector<int> rHash;
    int len = triangle.size() - 1;
    for(int i = len;i >= 0; i--){
        for(int j = 0; j < i + 1;j++){
            if(i == len){
                rHash.push_back(triangle[i][j]);
            }else{
                rHash.push_back(min(hash[len - i - 1][j + 1], hash[len - i - 1][j]) + triangle[i][j]);
            }
        }
        hash.push_back(rHash);
        rHash.clear();
    }
    return hash[len][0];
}
// 自顶向下
int minimumTotal(vector<vector<int>>& triangle) {
    vector<vector<int>> hash;
    vector<int> rHash;
    rHash.push_back(triangle[0][0]);
    hash.push_back(rHash);
    rHash.clear();
    for(int i = 1;i < triangle.size(); i++){
        for(int j = 0; j < i + 1;j++){
            if(j == 0){
                rHash.push_back(hash[i - 1][j] + triangle[i][j]);
            }else if(j == i){
                rHash.push_back(hash[i - 1][j - 1] + triangle[i][j]);
            }else{
                rHash.push_back(min(hash[i - 1][j - 1], hash[i - 1][j]) + triangle[i][j]);
            }
        }
        hash.push_back(rHash);
        if(i < triangle.size() - 1)
            rHash.clear();
    }
    int tmp = rHash[0];
    for(int i = 1;i < rHash.size(); i++){
        if(tmp > rHash[i])
            tmp = rHash[i];
    }
    return tmp;
}
```

### 🖌 递归与动规的关系

递归是一种程序的实现方式：函数的自我调用。

动态规划：是一种解决问 题的思想，大规模问题的结果，是由小规模问 题的结果运算得来的。动态规划可用递归的方式来实现\(Memorization Search\)。





