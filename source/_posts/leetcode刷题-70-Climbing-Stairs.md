---
title: leetcode刷题-70. Climbing Stairs
date: 2018-11-23 21:41:29
tags: [leetcode,动态规划]
categories: [leetcode,动态规划]
---

# 70. Climbing Stairs-爬楼梯

---

## 描述：
假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

注意：给定 n 是一个正整数。

示例 1：
```
输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。
1.  1 阶 + 1 阶
2.  2 阶
```
示例 2：
```
输入： 3
输出： 3
解释： 有三种方法可以爬到楼顶。
1.  1 阶 + 1 阶 + 1 阶
2.  1 阶 + 2 阶
3.  2 阶 + 1 阶
```

---

**思路：**

这道题目实际上跟**斐波那契数列**非常相似，假设梯子有n层，那么如何爬到第n层呢，因为每次只能爬1或2步，那么爬到第n层的方法要么是从第n-1层一步上来的，要不就是从n-2层2步上来的，所以递推公式非常容易的就得出了：**dp[n] = dp[n-1] + dp[n-2]**。 由于斐波那契额数列的求解可以用递归，所以最先尝试了递归，拿到OJ上运行，显示Time Limit Exceeded，就是说运行时间超了，因为递归计算了很多分支，效率很低，这里需要用动态规划 (Dynamic Programming) 来提高效率，代码如下：

代码一：

```c++
class Solution {
public:
    int climbStairs(int n) {
        if (n <= 1) return 1;
        vector<int> dp(n);
        dp[0] = 1; dp[1] = 2;
        //类似于斐波那契数列
        for (int i = 2; i < n; ++i) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp.back();
    }
};
```
**另外：**

>我们可以对空间进行进一步优化，我们只用两个整型变量a和b来存储过程值，首先将a+b的值赋给b，然后a赋值为原来的b，所以应该赋值为b-a即可。这样就模拟了上面累加的过程，而不用存储所有的值。

代码二：

```c++
class Solution {
public:
    int climbStairs(int n) {
        int a = 1, b = 1;
        while (n--) {
            b += a;
            a = b - a;
        }
        return a;
    }
};
```