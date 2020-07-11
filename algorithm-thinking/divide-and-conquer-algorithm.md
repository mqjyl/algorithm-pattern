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
* [归并排序](../algorithm/sort-algorithm.md#gui-bing-pai-xu-er-lu-gui-bing)和[快速排序](../algorithm/sort-algorithm.md#kuai-su-pai-xu)
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

### 2、大整数相乘

两个 n 位的大整数相乘，按照基线乘法（也就是笔算乘法或竖式计算法），算法的时间复杂度是 $$O(n^2)$$ ，基线乘法在 $$O(n^2)$$ 的复杂度上进行计算和向上传递进位，每计算一次单精度乘法都要计算和传递进位，这样的话就使得嵌套循环的顺序性很强，难以并行展开和实现。有一种改进的 Comba 乘法，和普通的笔算乘法很类似，只是每一次单精度乘法只是单纯计算乘法，不计算进位，进位留到每一列累加后进行。所以原来需要 $$n^2$$ 次进位，现在最多只需要 $$2n$$ 次即可。

然而这个问题可以用分治法来解决，即**Karatsuba算法**。

![](../.gitbook/assets/11.png)



算法：首先将 X 和 Y 分成A、B、C、D，此时将 X 和 Y 的乘积转化为图中的式子，把问题转化为求解式子的值。对于这个式子，我们一共要进行4 次乘法（AC、AD、BC、BD），所以a=4，建立递归方程：

$$
T(n) = 4 \times T(n / 2) + \theta(n)
$$

通过master定理可以求得该算法的时间复杂度为： $$T(n) = \theta(n ^ 2)$$ 。然后用加法来换取乘法：

![](../.gitbook/assets/13.png)

对于这个公式，一共进行了三次乘法（AC、BD、\(A+B\)\(C+D\)），因此，a=3，建立递归方程：

$$
T(n) = 3 \times T(n / 2) + \theta(n)
$$

通过master定理求得时间复杂度为： $$T(n) = O(n^{log_2 3 }) = O(n^{1.59})$$ 。

