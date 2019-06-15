---
title: leetcode刷题-11. Container With Most Water
date: 2018-11-28 22:53:05
tags: [leetcode,数组]
categories: [leetcode,数组]
---

# 11. Container With Most Water-盛最多水的容器

---

## 描述：

给定 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

**说明：** 你不能倾斜容器，且 n 的值至少为 2。<div align = center>
![图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/leetcode/question_11.jpg?q-sign-algorithm=sha1&q-ak=AKIDAo1tspwphcTrXVj2W29WzZffzFfpa5c3&q-sign-time=1543417200;1543419000&q-key-time=1543417200;1543419000&q-header-list=&q-url-param-list=&q-signature=88a6e762f29499510c18505700752a1f18c91853&x-cos-security-token=2bc82588c9883bce6d8e820373aa29a8543eb06010001)</div>

>图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。

示例:
```
输入: [1,8,6,2,5,4,8,3,7]
输出: 49
```

**思路：**

考虑用**双指针**法解题。维护两个指针f、l分别指向数组左右两端，则f和l构成的容器盛水体积计算公式为**min(height[f],height[l])*(l-f)**。可以看出盛水体积取决于容器边中较小的数字和容器底长度。若移动较大的数字，则容器底长度会变小，而盛水的最大高度不变，所以盛水体积不会变更大。因此需要移动两数中较小的数字，这样若中间出现特别大的数字，则有可能提高盛水体积。然后每次移动完更新此时的最大盛水体积，直到两指针相遇。

代码：

```c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int f = 0,l = height.size() - 1;
        int max = -1;
        while(f < l){
            int area = min(height[f],height[l])*(l-f);
            if(area > max)
                max = area;
            if(height[f]<height[l])
                f++;
            else
                l--;
        }
        return max;
    }
};
```