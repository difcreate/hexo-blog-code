---
title: leetcode刷题-215. Kth Largest Element in an Array
date: 2018-12-10 19:57:49
tags: [leetcode, 排序]
categories: [leetcode, 排序]
---

# 215. Kth Largest Element in an Array-数组中的第K个最大元素

---

##  描述：

在未排序的数组中找到第 k 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

示例 1:
```
输入: [3,2,1,5,6,4] 和 k = 2
输出: 5
```
示例 2:
```
输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
输出: 4
```
说明:
```
你可以假设 k 总是有效的，且 1 ≤ k ≤ 数组的长度。
```

---

代码一：
使用STL库函数 nth_element()，通过调用nth_element(start, start+n, end) 方法可以使第n大元素处于第n位置（从0开始,其位置是下标为 n的元素），并且比这个元素小的元素都排在这个元素之前，比这个元素大的元素都排在这个元素之后，但不能保证他们是有序的 

```c++
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        nth_element(nums.begin(),nums.end()-k,nums.end());
        return *(nums.end()-k);
    }
};
```

代码二：
直接排序，然后取出第K大即可 

```c++
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        sort(nums.begin(),nums.end());
        return nums[nums.size()-k];
    }
};
```

代码三：
借鉴快速排序的思想。每次选取一个 pivot，将大于 pivot 的数放在 pivot 左边，将小于 pivot 的数放在 pivot 右边。这时候，如果 pivot 正好是第 K 个数据，则 pivot 就是数组中的第 K 个最大元素。

```c++
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        int n=nums.size(),tleft=0,tright=n-1;
        while(tleft<tright){
            // 从大到小对数组进行快排
            int left=tleft,right=tright;
            int tmp=nums[left];
            while(left<right){
                while(right>left&&nums[right]>tmp) right--; // 从左往右找第一个比 pivot 小的数
                if(right>left){
                    nums[left++]=nums[right];
                }
                while(left<right&&nums[left]<=tmp) left++; // 从右往左找第一个比 pivot 大的数
                if(left<right) nums[right--]=nums[left];
            }
            nums[left]=tmp;
            // pivot 此时位于索引 left 处，n - k 表示 pivot 是第几大的数
            if(left==n-k) return tmp;
            // 第 k 大的数在 pivot 右边，问题转化为找右边数组第 (k -left) 大的元素
            else if(left<n-k){
                tleft=left+1;
                tright=tright;
            }
            // 第 k 大的数在 pivot 左边，问题转化为找左边数组第 k 大的元素
            else{
                tleft=tleft;
                tright=left-1;
            }
        }
        return nums[tleft];
    }
};
```