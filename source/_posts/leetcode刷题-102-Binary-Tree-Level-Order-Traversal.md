---
title: leetcode刷题-102. Binary Tree Level Order Traversal
date: 2018-12-06 17:22:11
tags: [leetcode,树]
categories: [leetcode,树]
---

# 102. Binary Tree Level Order Traversal-二叉树的层次遍历

---

## 描述：
给定一个二叉树，返回其按层次遍历的节点值。 （即逐层地，从左到右访问所有节点）。

例如:
给定二叉树: [3,9,20,null,null,15,7],
```
    3
   / \
  9  20
    /  \
   15   7
```
返回其层次遍历结果：
```
[
  [3],
  [9,20],
  [15,7]
]
```

---

**递归版：**

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
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> result;
        traverse(root, 1, result);
        return result;
    }
    void traverse(TreeNode *root, size_t level, vector<vector<int>> &result) {
        if (!root) return;
        // 如果已经递归到另一层，则给二维数组result添加一个空的一位数组
        <!-- ```
        结果数组输出格式：
            [
            [3],
            [9,20],
            [15,7]
            ]
        ``` -->
        if (level > result.size())
            result.push_back(vector<int>());

        result[level-1].push_back(root->val);
        traverse(root->left, level+1, result);
        traverse(root->right, level+1, result);
    }
};
```

**迭代版：用队列**

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
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> vec;
        if(root==NULL){
            return vec;
        }
         
        queue<TreeNode*> que;
        que.push(root);
        
        while(!que.empty()){
            int size=que.size();
            vector<int> vec_temp;
            //每一次都将某一层的节点值添加到这一层的一位数组中
            while(size--)
            {
                TreeNode* t = que.front();
                vec_temp.push_back(que.front()->val);
                que.pop();
                
                if(t->left){
                    que.push(t->left);
                }
                 
                if(t->right){
                    que.push(t->right);
                }
            }
            //二维结果数组添加每一层的一位数组
            vec.push_back(vec_temp);
        }
        return vec;
    }
};
```