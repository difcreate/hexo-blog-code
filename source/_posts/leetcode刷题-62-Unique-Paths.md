---
title: leetcode刷题-62. Unique Paths
date: 2018-11-27 22:07:31
tags: [leetcode,动态规划]
categories: [leetcode,动态规划]
---

# 62. Unique Paths-不同路径

---

## 描述：

一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

问总共有多少条不同的路径？<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/leetcode/question_62.png?q-sign-algorithm=sha1&q-ak=AKID2vZhp1cvd42VAikuWAywg73c8X0YvzTj&q-sign-time=1543417433;1543419233&q-key-time=1543417433;1543419233&q-header-list=&q-url-param-list=&q-signature=aebbb05c9130983eb09642a9bfe67eb7f8298dd2&x-cos-security-token=73c5e962610da5b65c10947356559eb21fe46a6e10001)</div>
>例如，上图是一个7 x 3 的网格。有多少可能的路径？

说明：m 和 n 的值均不超过 100。

示例 1:
```
输入: m = 3, n = 2
输出: 3
解释:
从左上角开始，总共有 3 条路径可以到达右下角。
1. 向右 -> 向右 -> 向下
2. 向右 -> 向下 -> 向右
3. 向下 -> 向右 -> 向右
```
示例 2:
```
输入: m = 7, n = 3
输出: 28
```

---

**思路：**

这道题跟之前那道 Climbing Stairs 很类似，那道题是说可以每次能爬一格或两格，问到达顶部的所有不同爬法的个数。而这道题是每次可以向下走或者向右走，求到达最右下角的所有不同走法的个数。那么跟爬梯子问题一样，我们需要用**动态规划**来解，我们可以维护一个二维数组nums，其中nums[i][j]表示到当前位置不同的走法的个数，然后可以得到**状态转移方程: nums[i][j] = nums[i - 1][j] + nums[i][j - 1]**。

代码一：

```c++
//时间复杂度O(m*n),空间复杂度O(m*n)
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<int> num(n,1);
        vector<vector<int>> nums(m,num);
        //nums[i][j] = nums[i - 1][j] + nums[i][j - 1];
        for (int i = 0 ;i < m;i++) {
            for (int j = 0; j < n; j++) {
                if(i == 0 || j == 0)
                    nums[i][j] = 1;
                else
                    nums[i][j] = nums[i - 1][j] + nums[i][j - 1];
            }
        }
        return nums[m - 1][n - 1];
    }
};
```

为了节省空间，我们使用一维数组nums，一行一行的刷新也可以。

代码二：

```c++
//时间复杂度O(m*n),空间复杂度O(n)
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<int> dp(n, 1);
        for (int i = 1; i < m; ++i) {
            for (int j = 1; j < n; ++j) {
                dp[j] += dp[j - 1]; 
            }
        }
        return dp[n - 1];
    }
};
```
