---
title: leetcode刷题-100.Same Tree
date: 2018-11-14 14:58:20
tags: [leetcode,树,C++]
categories: [leetcode,树]
---

# 100. Same Tree-相同的树

---

给定两个二叉树，编写一个函数来检验它们是否相同。

如果两个树在结构上相同，并且节点具有相同的值，则认为它们是相同的。

示例 1:

```
输入:       1         1
          / \       / \
         2   3     2   3

        [1,2,3],   [1,2,3]

输出: true
```

示例 2:

```
输入:      1          1
          /           \
         2             2

        [1,2],     [1,null,2]

输出: false
```

示例 3:

```
输入:       1         1
          / \       / \
         2   1     1   2

        [1,2,1],   [1,1,2]

输出: false
```

---

# 递归版：

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
 //递归版，时间复杂度O(n),空间复杂度O(logn)
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if (!p && !q) return true; //终止条件
        if (!p || !q) return false; //剪枝
        return p->val == q->val     //三方合并
        && isSameTree(p->left, q->left) 
        && isSameTree(p->right, q->right);
    }
};
```

# 迭代版：

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
    bool isSameTree(TreeNode* p, TreeNode* q) {
        stack<TreeNode*> s;
        s.push(p);
        s.push(q);
        while(!s.empty()) {
            p = s.top(); s.pop();
            q = s.top(); s.pop();
            if (!p && !q) continue;
            if (!p || !q) return false;
            if (p->val != q->val) return false;
            s.push(p->left);
            s.push(q->left);
            s.push(p->right);
            s.push(q->right);
        }
        return true;
    }
};
```




