---
description: 二叉树相关的算法实现。
---

# 二叉树

### 知识点

#### 1、二叉树的存储

* **顺序存储**：这种情况之适合完全二叉树
* **链式存储**：二叉链表和三叉链表（一个parent域），在含有 n 个结点的二叉链表中有（n+1）个空链域。

#### 2、二叉树的创建

> 唯一确定一颗二叉树的方法：中序加前序 或 中序加后序，此时必须假设树中没有重复的元素或者有唯一的结点编号。

#### [**Construct Binary Tree from Preorder and Inorder Traversal**](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)\*\*\*\*

```cpp
TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
    if(inorder.size() <= 0){
        return NULL;
    }
    int tmp = 0;
    for(int i = 0;i < inorder.size();i ++){
        if(preorder[0] == inorder[i]){
            tmp = i;
            break;
        }
    }
    TreeNode *treeNode = new TreeNode(preorder[0]);
    if(tmp > 0){
        // 左闭右开区间
        vector<int> inorder_left(inorder.begin(), inorder.begin() + tmp);
        vector<int> preorder_left(preorder.begin() + 1, preorder.begin() + tmp + 1);
        treeNode->left = buildTree(preorder_left, inorder_left);
    }
    if(inorder.size() > tmp + 1){
        vector<int> inorder_right(inorder.begin() + tmp + 1, inorder.end());
        vector<int> preorder_right(preorder.begin() + tmp + 1, preorder.end());
        treeNode->right = buildTree(preorder_right, inorder_right);
    }
    return treeNode;
}
```

#### \*\*\*\*[**Construct Binary Tree from Inorder and Postorder Traversal**](https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)\*\*\*\*

```cpp
TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
    if(inorder.size() == 0){
        return NULL;
    }
    if(inorder.size() == 1){
        return new TreeNode(inorder[0]);
    }
    int tmp = 0;
    for(int i = 0;i < inorder.size();i ++){
        if(postorder.back() == inorder[i]){
            tmp = i;
            break;
        }
    }
    TreeNode *treeNode = new TreeNode(inorder[tmp]);
    if(tmp > 0){
        // 左闭右开区间
        vector<int> inorder_left(inorder.begin(), inorder.begin() + tmp);
        vector<int> postorder_left(postorder.begin(), postorder.begin() + tmp);
        treeNode->left = buildTree(inorder_left, postorder_left);
    }
    if(inorder.size() > tmp + 1){
        vector<int> inorder_right(inorder.begin() + tmp + 1, inorder.end());
        vector<int> postorder_right(postorder.begin() + tmp, postorder.end() - 1);
        treeNode->right = buildTree(inorder_right, postorder_right);
    }
    return treeNode;
}
```

#### 3、二叉树的遍历

> **前序遍历**：**先访问根节点**，再前序遍历左子树，再前序遍历右子树 **中序遍历**：先中序遍历左子树，**再访问根节点**，再中序遍历右子树 **后序遍历**：先后序遍历左子树，再后序遍历右子树，**再访问根节点**
>
> * 以根访问顺序决定是什么遍历
> * 左子树都是优先右子树

#### \*\*\*\*[**二叉树的前序遍历**](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)\*\*\*\*

```cpp
// 递归遍历
/*
vector<int> preorderTraversal(TreeNode* root) {
    vector<int> result;
    if(!root)
        return result;
    result.push_back(root->val);
    if(root->left){
        vector<int> result_left = preorderTraversal(root->left);
        result.insert(result.end(), result_left.begin(), result_left.end());
    } 
    if(root->right){
        vector<int> result_right = preorderTraversal(root->right);
        result.insert(result.end(), result_right.begin(), result_right.end());
    } 
    return result;
}
*/    
```



