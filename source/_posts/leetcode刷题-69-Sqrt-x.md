---
title: leetcode刷题-69. Sqrt(x)
date: 2018-12-01 22:27:20
tags: [leetcode,数学]
categories: [leetcode,数学]
---

# 69. Sqrt(x)-x的平方根

---

## 描述：
实现 int sqrt(int x) 函数。

计算并返回 x 的平方根，其中 x 是非负整数。

由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

示例 1:
```
输入: 4
输出: 2
```
示例 2:
```
输入: 8
输出: 2
说明: 8 的平方根是 2.82842..., 
     由于返回类型是整数，小数部分将被舍去。
```

---

**思路：**

算一个候选值的平方，然后和x比较大小，为了缩短查找时间，我们采用二分搜索法来找平方根

代码：

```c++
  class Solution {
  public:
      int mySqrt(int x) {
          if (x <= 1) return x;
          int left = 0, right = x;
          while (left < right) {
              int mid = left + (right - left) / 2;
              if (x / mid >= mid) left = mid + 1;
              else right = mid;
         }
         return right - 1;
     }
 };
```