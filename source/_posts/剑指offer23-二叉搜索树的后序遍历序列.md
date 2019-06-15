---
title: 剑指offer23-二叉搜索树的后序遍历序列
date: 2019-05-23 20:42:47
tags: [剑指offer,C++]
categories: [剑指offer,二叉树]
---
# 二叉搜索树的后序遍历序列


---

**题目描述**

输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。

---


**C++解题思路**

利用递归，后序遍历中最后一位是根节点，然后将序列前面分成两部分，前面部分比根节点小的为左子树，中间部分比根节点大的为右子树；要考虑最后一层的孩子节点为单孩子还是双孩子节点

```C++
class Solution {
public:
    bool VerifySquenceOfBST(vector<int> sequence) {
        if(sequence.size() == 0){
            return false;
        }
        return judge(sequence, 0, sequence.size() - 1);
    }
    bool judge(vector<int> &sequence, int left, int right){
        if(left >= right){ //考虑有左右孩子和单孩子的请况
            return true;
        }
        int mid = right - 1;
        while(sequence[mid] > sequence[right]){
            mid--;
        }
        int i = left;
        while (i < mid && sequence[i] < sequence[right]){
            i++;
        }
        if (i < mid){
            return false;
        }
        ///  这样我们就划分出区间
        ///  [left, mid] 是左子树
        ///  [mid + 1, right - 1] 是右子树
        ///  right 是根节点
        return judge(sequence, left, mid) && judge(sequence, mid + 1, right - 1);
    }
};
```
