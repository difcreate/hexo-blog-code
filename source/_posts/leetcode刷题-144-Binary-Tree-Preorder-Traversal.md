---
title: leetcode刷题-144. Binary Tree Preorder Traversal
date: 2018-11-29 20:10:26
tags: [leetcode,树]
categories: [leetcode,树]
---

# 144. Binary Tree Preorder Traversal-二叉树的前序遍历

---

## 描述：

给定一个二叉树，返回它的 前序 遍历。

 示例:
```
输入: [1,null,2,3]  
   1
    \
     2
    /
   3 

输出: [1,2,3]
```
>进阶: 递归算法很简单，你可以通过迭代算法完成吗？

---

非递归版-使用栈解题
代码一：

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
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> result;
        stack<const TreeNode *> s;
        if (root != nullptr) s.push(root);
        while (!s.empty()) {
            const TreeNode *p = s.top();
            s.pop();
            result.push_back(p->val);
            if (p->right != nullptr) s.push(p->right);
            if (p->left != nullptr) s.push(p->left);
        }
        return result;
    }
};
```

递归版

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
class Solution {
public:
    
    void preorder(TreeNode *root, vector<int> &result){
        if(root != nullptr) {
            result.push_back(root->val);
            preorder(root->left,result);
            preorder(root->right,result);
        }
    }
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> result;
        preorder(root,result);
        return result;
    }
};
```