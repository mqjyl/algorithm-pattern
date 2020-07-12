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

### 2、[大整数相乘](https://leetcode-cn.com/problems/multiply-strings/)

两个 $$n$$ 位的大整数相乘，按照基线乘法（也就是笔算乘法或竖式计算法），算法的时间复杂度是 $$O(n^2)$$ ，基线乘法在 $$O(n^2)$$ 的复杂度上进行计算和向上传递进位，每计算一次单精度乘法都要计算和传递进位，这样的话就使得嵌套循环的顺序性很强，难以并行展开和实现。有一种改进的 Comba 乘法，和普通的笔算乘法很类似，只是每一次单精度乘法只是单纯计算乘法，不计算进位，进位留到每一列累加后进行。所以原来需要 $$n^2$$ 次进位，现在最多只需要 $$2n$$ 次即可。

然而这个问题可以用分治法来解决，时间复杂度可降至 $$O(n^{1.59})$$ ，即**Karatsuba算法**。

![](../.gitbook/assets/11.png)

算法：首先将 X 和 Y 分成A、B、C、D，此时将 X 和 Y 的乘积转化为图中的式子，把问题转化为求解式子的值。对于这个式子，我们一共要进行4 次乘法（AC、AD、BC、BD），所以 $$a = 4$$ ，建立递归方程：

$$
T(n) = 4 \times T(n / 2) + \theta(n)
$$

通过master定理可以求得该算法的时间复杂度为： $$T(n) = \theta(n ^ 2)$$ 。然后用加法来换取乘法：

$$
\begin{aligned}
XY & = (A\times 2^{\frac{n}{2}} + B)(C\times 2^{\frac{n}{2}} + D) \\
& = AC\times 2^n + ((A+B)(C+D)-AC-BD)\times 2^{\frac{n}{2}} + BD
\end{aligned}
$$

对于这个公式，一共进行了三次乘法（AC、BD、\(A+B\)\(C+D\)），因此， $$a = 3$$ ，建立递归方程：

$$
T(n) = 3 \times T(n / 2) + \theta(n)
$$

通过master定理求得时间复杂度为： $$T(n) = O(n^{log_2 3 }) = O(n^{1.59})$$ 。

另外常见的大数相乘算法还有 [**快速傅里叶变换算法**](https://en.wikipedia.org/wiki/Fast_Fourier_transform)**（** **fast Fourier transform** \(**FFT**\)**）。**

**这道题除了按普通的竖式求解（**即遍历 `num2` 每一位与 `num1` 进行相乘，将每一步的结果进行累加）外，还可以**可以通过优化竖式来求解，两种优化方法：**

> 一、通过两数相乘时，乘数某位与被乘数某位相乘，与产生结果的位置的规律来完成。
>
> 具体规律如下：
>
> * 乘数 `num1` 位数为 $$M$$ ，被乘数 `num2` 位数为 $$N$$ ， `num1 x num2` 结果 `res` 最大总位数为 $$M+N$$ 。
> * `num1[i] x num2[j]` 的结果为 `tmp`\(位数为两位，`"0x","xy"`的形式\)，其第一位位于 `res[i+j]`，第二位位于 `res[i+j+1]`。

![](../.gitbook/assets/18.png)

> 二、模拟乘法，将所有数据不单独进位（可直接存入数组），最后统一进位。（实现）

![](../.gitbook/assets/17.png)

```cpp
string multiply(string num1, string num2) {
    if(num1[0] == '0' || num2[0] == '0'){
        return "0";
    }
    string result;
    int carry = 0, sum = 0;
    int len1 = num1.size();
    int len2 = num2.size();
    for(int i = len2 - 1;i >= 0;i--){
        sum = 0;
        for(int j = len1 - 1;j >= 0 && i + (len1 - 1 - j) < len2;j--){
            sum += (num1[j] - '0') * (num2[i + (len1 - 1 - j)] - '0');
        }
        sum += carry;
        carry = sum / 10;
        result.insert(0, 1, char(sum % 10 + '0'));
    }
    for(int i = len1 - 2;i >= 0;i--){
        sum = 0;
        for(int j = 0;j < len2 && i - j >= 0;j++){
            sum += (num2[j] - '0') * (num1[i - j] - '0');
        }
        sum += carry;
        carry = sum / 10;
        result.insert(0, 1, char(sum % 10 + '0'));
    }
    while(carry > 0){
        result.insert(0, 1, char(carry % 10 + '0'));
        carry = carry / 10;
    }
    return result;
}
```

