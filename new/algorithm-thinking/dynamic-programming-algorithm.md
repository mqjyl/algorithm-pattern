---
description: 学习动态规划的算法思维，总结常见的题型。
---

# 动态规划

## :pencil2: 动态规划

### :paintbrush: 背景

从一道例题开始：[**Triangle**](https://leetcode-cn.com/problems/triangle/)

> 给定一个三角形，找出自顶向下的最小路径和。每一步只能移动到下一行中相邻的结点上。

> 例如，给定三角形：

> ```
> [                        [
>      [2],                         [2]
>     [3,4],                       [3,4]
>    [6,5,7],                    [6,5,5,7]        (转换的二叉树)
>   [4,1,8,3]                [4,1,1,8,1,8,8,3]
> ]                        ]
>
> 搜索的路径是三角形转换而来二叉树的前序遍历。
>
> 自底向上方法中：当前的位置的最优值为它相邻的两个节点中的最优值
> ```

> 自顶向下的最小路径和为 11（即，2 + 3 + 5 + 1 = 11）。

#### 方法一：`DFS`（遍历——回溯，分治）——暴力搜索

> 遍历和分治法都是暴力搜索，搜索的路径是三角形转换而来二叉树的前序遍历。

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

**方法二：记忆化搜索——优化 `DFS`，缓存已经被计算的值（空间换时间），本质上是动态规划**

动态规划就是把大问题变成小问题，并解决了小问题重复计算的方法称为动态规划。

> 动态规划和 `DFS` 区别
>
> * 二叉树 子问题是没有交集，所以大部分二叉树都用递归或者分治法，即 `DFS`，就可以解决
> * 像 triangle 这种是有重复走的情况，**子问题是有交集**，所以可以用动态规划来解决
>
> **可以用二维数组，可以借助一维数组（长度为三角形的行数），也可以原地操作**

{% tabs %}
{% tab title="自底向上" %}
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
            }else{ // hash[len-i-1][j + 1], hash[len-i-1][j]这就是相邻的节点的坐标
                rHash.push_back(min(hash[len - i - 1][j + 1], 
                        hash[len - i - 1][j]) + triangle[i][j]);
            }
        }
        hash.push_back(rHash);
        rHash.clear();
    }
    return hash[len][0];
}
```
{% endtab %}

{% tab title="自顶向下" %}
```cpp
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
                rHash.push_back(min(hash[i - 1][j - 1], hash[i - 1][j]) 
                 + triangle[i][j]);
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
{% endtab %}
{% endtabs %}

### :paintbrush: 递归与动规的关系

递归是一种程序的实现方式：函数的自我调用。

动态规划：是一种解决问 题的思想，大规模问题的结果，是由小规模问 题的结果运算得来的。动态规划可用递归的方式来实现(Memorization Search)。

### :paintbrush: 使用场景

满足两个条件

* 满足以下条件之一
*
  * 求最大/最小值（Maximum/Minimum ）
  * 求是否可行（Yes/No ）
  * 求可行个数（Count(\*) ）
* 满足不能排序或者不能交换（Can not sort / swap ）

> 如题：[longest-consecutive-sequence](https://leetcode-cn.com/problems/longest-consecutive-sequence/) 位置可以交换，所以不用动态规划。

### :paintbrush: 四点要素

1. 状态 State ：存储小规模问题的结果
2. 方程 Function ：状态之间的联系，怎么通过小的状态，来算大的状态
3. 初始化 Initialization ：最极限的小状态是什么，起点
4. 答案 Answer ：最大的那个状态是什么，终点、

**动态规划的设计，其实就是利用最优子结构和重叠子问题性质对穷举法进行优化，通过将中间结果保存在数组中，实现用空间来换取时间交换，实现程序的快速运行。（动态规划求解时，一般都会转化为网格进行求解，而且因为是空间换时间（避免了子问题的重复计算），因此一般迭代求解）。**

### :paintbrush: 滚动数组

**滚动数组**是DP中的一种编程思想。简单的理解就是让数组滚动起来，每次都使用固定的几个存储空间，来达到压缩，节省存储空间的作用，主要应用在递推或动态规划中（如01背包问题）。因为DP题目是一个自底向上的扩展过程，我们常常需要用到的是连续的解，前面的解往往可以舍去。所以用滚动数组优化是很有效的。利用滚动数组的话在N很大的情况下可以达到压缩存储的作用。

## :pencil2: 常见四种类型

1. Matrix DP (10%)
2. Sequence (40%)
3. Two Sequences DP (40%)
4. Backpack (10%)

> 注意点
>
> * 贪心算法大多题目靠背答案，所以如果能用动态规划就尽量用动规，不用贪心算法

### :pen\_ballpoint: 矩阵类型（10%）

#### 1、[minimum-path-sum](https://leetcode-cn.com/problems/minimum-path-sum/)

给定一个包含非负整数的 _m_ x _n_ 网格，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

$$
function: f[x][y] = min(f[x-1][y], f[x][y-1]) + A[x][y]
$$

```cpp
// 原地操作
int minPathSum(vector<vector<int>>& grid) {
    int m = grid.size();
    int n = grid[0].size();
    for(int i = 0;i < m;i++){
        for(int j = 0;j < n;j++){
            if(i == 0 && j == 0){
                grid[i][j] = grid[i][j];
            }else if(i == 0 && j > 0){
                grid[i][j] = grid[i][j - 1] + grid[i][j];
            }else if(j == 0 && i > 0){
                grid[i][j] = grid[i - 1][j] + grid[i][j];
            }else{
                grid[i][j] = min(grid[i - 1][j], grid[i][j - 1]) + grid[i][j];
            }
        }
    }
    return grid[m - 1][n - 1];
}
```

#### 2、[unique-paths](https://leetcode-cn.com/problems/unique-paths/)

一个机器人位于一个 m x n 网格的左上角 。机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角。问总共有多少条不同的路径？

$$
function: f[x][y] = f[x-1][y], f[x][y-1]
$$

```cpp
int uniquePaths(int m, int n) {
    vector<int> dp(n, 1);
    for(int i = 1;i < m;i++){
        for(int j = 1;j < n;j++){
            dp[j] = dp[j - 1] + dp[j];
        }
    }
    return dp[n - 1];
}
```

#### 3、[unique-paths-ii](https://leetcode-cn.com/problems/unique-paths-ii/)

&#x20;在上一道题的基础上增加障碍物，网格中的障碍物和空位置分别用 `1` 和 `0` 来表示。

```cpp
int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
    if(obstacleGrid[0][0] == 1)
        return 0;
    int m = obstacleGrid.size();
    int n = obstacleGrid[0].size();
    std::vector<int> dp(n, 0);
    for(int i = 0;i < m;i++){
        for(int j = 0;j < n;j++){
            if(i == 0 && j == 0){
                dp[j] = 1;
            }else if(i == 0 && j > 0){  // 初始化
                if(obstacleGrid[i][j] == 1 || dp[j - 1] == 0){
                    dp[j] = 0;
                }else{
                    dp[j] = 1;
                }
            }else if(j == 0 && i > 0){
                if(obstacleGrid[i][j] == 1){
                    dp[j] = 0;
                }
            }else{
                if(obstacleGrid[i][j] == 1){
                    dp[j] = 0;
                }else{
                    dp[j] = dp[j] + dp[j - 1];
                }
            }
        }
    }
    return dp[n - 1];
}
```

### :pen\_ballpoint: 序列类型（40%）

#### 1、[climbing-stairs](https://leetcode-cn.com/problems/climbing-stairs/)

假设你正在爬楼梯。需要 _n_ 阶你才能到达楼顶。

```cpp
int climbStairs(int n) {
    if(n == 1 || n == 2)
        return n;
    int x = 1, y = 2;
    for(int i = 2;i < n;i++){
        int tmp = x + y;
        x = y;
        y = tmp;
    }
    return y;
}
```

#### 2、[jump-game](https://leetcode-cn.com/problems/jump-game/)

给定一个非负整数数组，你最初位于数组的第一个位置。 数组中的每个元素代表你在该位置可以跳跃的最大长度。 判断你是否能够到达最后一个位置。

```cpp
bool canJump(vector<int>& nums) {
    int curr_end = 0;
    for(int i = 0;i < nums.size();i++){
        if(curr_end >= i){
            curr_end = curr_end > (i + nums[i]) ? curr_end : (i + nums[i]);
        }else{
            curr_end = -1;
            break;
        }
    }
    return curr_end == -1 ? false : true;
}
```

#### 3、[机器人达到指定位置方法数](https://www.nowcoder.com/questionTerminal/54679e44604f44d48d1bcadb1fe6eb61)

假设有排成一行的N个位置，记为1\~N，开始时机器人在M位置，机器人可以往左或者往右走，如果机器人在1位置，那么下一步机器人只能走到2位置，如果机器人在N位置，那么下一步机器人只能走到N-1位置。规定机器人只能走k步，最终能来到P位置的方法有多少种。由于方案数可能比较大，所以答案需要对`1e9+7`取模。

{% tabs %}
{% tab title="状态穷举" %}
```cpp
const int prime = 1e9 + 7;

long long getSolutions(int n, int m, int k, int p) {
	vector<vector<long long>> solutions(k + 1, vector<long long>(n + 2));
	// base case
	for (int i = 0; i < n + 2; ++i) {
		solutions[0][i] = 0;
	}
	solutions[0][m] = 1;
	for (int i = 1; i < k + 1; ++i) {
		for (int j = 1; j < n + 1; ++j) {
			solutions[i][j] = solutions[i - 1][j - 1] + solutions[i - 1][j + 1];
			solutions[i][j] %= prime;
		}
	}
	return solutions[k][p];
}
```
{% endtab %}

{% tab title="优化" %}
```cpp
long long getSolutions(int n, int m, int k, int p) {
    vector<long long> curr(n + 2);
    vector<long long> last(n + 2);
    // base case
    for (int i = 0; i < n + 2; ++i) {
        last[i] = 0;
    }
    last[m] = 1;
    for (int i = 1; i < k + 1; ++i) {
        for (int j = 1; j < n + 1; ++j) {
            curr[j] = last[j - 1] + last[j + 1];
            curr[j] %= prime;
        }
        for (int j = 1; j < n + 1; ++j) {
            last[j] = curr[j];
        }
    }
    return curr[p];
}
```
{% endtab %}
{% endtabs %}
