---
title: leetcode刷题-35. Search Insert Position
date: 2018-11-25 23:00:59
tags: [leetcode,查找]
categories: [leetcode,查找]
---

# 35. Search Insert Position-搜索插入位置

---

## 描述：

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

你可以假设数组中无重复元素。

示例 1:
```
输入: [1,3,5,6], 5
输出: 2
```
示例 2:
```
输入: [1,3,5,6], 2
输出: 1
```
示例 3:
```
输入: [1,3,5,6], 7
输出: 4
```
示例 4:
```
输入: [1,3,5,6], 0
输出: 0
```

---

代码一：

```c++
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        if(nums[0]>target||nums.empty())
            return 0;
        for(int i=0;i<nums.size();i++)
        {
            if(nums[i]==target)
                return i;
            if(nums[i]<target&&nums[i+1]>target)
            {
                return i+1;
            }
        }
        return nums.size();
    }
};
```

代码二：

```c++
class Solution {
public:
    int searchInsert(int A[], int n, int target) {
        if(A[0] > target)
            return 0;
        for(int i = 0; i < n; ++i){
            if(A[i] == target)
                return i;
            if(A[i] < target && A[i+1] > target)
                return i + 1;
        }
        return n;
    }
};
```