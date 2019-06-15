---
title: 剑指offer22-从上往下打印二叉树
date: 2019-05-22 11:35:12
tags: [剑指offer,C++]
categories: [剑指offer,队列,二叉树]
---
# 从上往下打印二叉树


---

**题目描述**

从上往下打印出二叉树的每个节点，同层节点从左至右打印

---


**C++解题思路**

在队列中插入结束标识来标识当前层结束

```C++
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/

#include<queue>

class Solution {
public:
    vector<int> PrintFromTopToBottom(TreeNode* root) {
        vector<int> res;
        if(!root) return res;
        queue<TreeNode* > q;
        q.push(root);
        q.push(NULL);// 在队列中插入结束标识来表示当前层结束
        while(!q.empty()){
            TreeNode* node = q.front();
            q.pop();
            if(node){
                res.push_back(node->val);
                if(node->left != NULL)
                    q.push(node->left);
                if(node->right != NULL)
                    q.push(node->right);
            }
            else if(!q.empty())
                q.push(NULL);
        }
        return res;
    }
};
```