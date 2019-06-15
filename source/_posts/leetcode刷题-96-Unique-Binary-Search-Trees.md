---
title: leetcode刷题-96. Unique Binary Search Trees
date: 2018-11-25 22:48:08
tags: [leetcode,树,动态规划]
categories: [[leetcode,树],[leetcode,动态规划]]
---

# 96. Unique Binary Search Trees-不同的二叉搜索树

---

## 描述：

给定一个整数 n，求以 1 ... n 为节点组成的二叉搜索树有多少种？

示例:
```
输入: 3
输出: 5
解释:
给定 n = 3, 一共有 5 种不同结构的二叉搜索树:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3

```

---

**思路：**

这道题我们可以看出一个规律就是当数组为1,2,3...,n时，基于以下原则的构建的BST树具有唯一性：**以i为根节点的树，其左子树由[1,i-1],组成，其右子树由[i+1,n]组成。**

此问题可以看作是一维动态规划问题，可以看出，以i为根节点的树的个数等于：**左子树的个数乘以右子树的个数(其中空子树和单节点树的个数都为1)**
公式表达为：$$f(i) = \sum_{k=1}^{i}f(k-1)*f(i-k)$$

代码：

```c++
//时间复杂度O(n^2),空间复杂度O(n)
class Solution {
public:
    int numTrees(int n) {
        vector<int> f(n + 1, 0);
        f[0] = 1;
        f[1] = 1;
        for(int i = 2; i <= n; ++i){
            for(int k = 1; k <= i; ++k){
                f[i] += f[k-1] * f[i-k];
            }
        }
        return f[n];
    }
};
```
