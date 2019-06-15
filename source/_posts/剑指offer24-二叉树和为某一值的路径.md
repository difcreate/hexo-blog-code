---
title: 剑指offer24-二叉树和为某一值的路径
date: 2019-05-23 21:30:05
tags: [剑指offer,C++]
categories: [剑指offer,二叉树]
---

# 二叉树和为某一值的路径


---

**题目描述**

输入一颗二叉树的跟节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。(注意: 在返回值的list中，数组长度大的数组靠前)

---


**C++解题思路**

用个递归来实现，先序遍历;
- 每次访问一个节点，那么就将当前权值求和
- 如果当前权值和与期待的和一致，那么说明我们找到了一个路径，保存或者输出
- 否则的话就递归其左右孩子节点 

> 这里需要注意一个问题，就是递归退出的时候，权值和的信息是保存在递归栈中的会恢复，但是我们保存的路径是无法恢复的，那么我们就需要在递归返回时将数据弹出。


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
class Solution {
public:
    vector<vector<int> > ans;
    vector<vector<int> > FindPath(TreeNode* root,int target){
        if(root == NULL)
            return ans;
        vector<int> path;
        FindToPath(root, target, path, 0);
        return ans;
    }
    void FindToPath(TreeNode* root, int target, vector<int> path, int currentsum){
        currentsum += root->val;
        path.push_back(root->val);
        if(target == currentsum && root->left == NULL && root->right == NULL)
            ans.push_back(path);
        if(root->left != NULL)
            FindToPath(root->left, target, path, currentsum);
        if(root->right != NULL)
            FindToPath(root->right, target, path, currentsum);
        //  此处不需要恢复currentSum和path的值:                                  
        //  因为currentSum作为参数在函数递归调用返回时会自动恢复                 
        //  而如果作为静态局部变量存储则需要进行恢复                             
        //  currentSum -= root->val;                                               
        //  path.pop_back( );
    }
};
```