---
description: 五大常用算法设计思想之一：分治算法，介绍其思想和应用场景，提供经典题目的解答。
---

# 分治法

> 分治法即“分而治之”，就是把一个复杂的问题分成两个或更多的相同或相似的子问题，再把子问题分成更小的子问题……直到最后子问题可以简单的直接求解，原问题的解即子问题的解的合并。

## **可使用分治法求解的一些经典问题**

* 二分搜索
* 大整数乘法
* Strassen矩阵乘法
* 棋牌覆盖
* 合并排序和快速排序
* 线性时间选择
* 最接近点对问题
* 循环赛日程表
* 汉诺塔

## 算法实现

### 1、[二叉树DFS深度搜索——自下而上](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)

```cpp
vector<int> preorderTraversal(TreeNode* root) {
    vector<int> result;
    // 返回条件(NULL)
    if(!root)
        return result;
    // 分治(Divide)
    vector<int> result_left, result_right;
    if(root->left){
        result_left = preorderTraversal(root->left);
    } 
    if(root->right){
        result_right = preorderTraversal(root->right);
    } 
    // 合并结果(Conquer)
    result.push_back(root->val);
    result.insert(result.end(), result_left.begin(), result_left.end());
    result.insert(result.end(), result_right.begin(), result_right.end());
    return result;
}  
```



