---
title: leetcode刷题-110. Balanced Binary Tree
date: 2018-11-27 18:21:10
tags: [leetcode,树]
categories: [leetcode,树]
---

# 110. Balanced Binary Tree-平衡二叉树

---

## 描述：

给定一个二叉树，判断它是否是高度平衡的二叉树。

本题中，一棵高度平衡二叉树定义为：

一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过1。

示例 1:
```
给定二叉树 [3,9,20,null,null,15,7]

    3
   / \
  9  20
    /  \
   15   7
返回 true 。
```
示例 2:
```
给定二叉树 [1,2,2,3,3,null,null,4,4]

       1
      / \
     2   2
    / \
   3   3
  / \
 4   4
返回 false 。
```

---

**代码一：**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
 //时间复杂度O(n),空间复杂度O(logn)
class Solution {
public:
    bool isBalanced(TreeNode* root) {
     return balancedHeight (root) >= 0;
    }
    /**
    * Returns the height of `root` if `root` is a balanced tree,
    * otherwise, returns `-1`.
    */
    int balancedHeight (TreeNode* root) {
        if (root == nullptr) return 0; // 终止条件
        int left = balancedHeight (root->left);
        int right = balancedHeight (root->right);
        if (left < 0 || right < 0 || abs(left - right) > 1) return -1; // 剪枝
        return max(left, right) + 1; // ̸三方合并
    }
};
```