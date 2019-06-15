---
title: 剑指offer33-丑数
date: 2019-05-27 11:35:19
tags: [剑指offer,C++]
categories: [剑指offer,字符串]
---

# 丑数


---

## 题目描述

把只包含质因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。

---


## 解题思路

### 暴力破解法

最简单直接的方法，就是逐个判断每个整数是不是丑数，循环所有数字，判断它是不是丑数 首先我们需要判断某个整数number是不是丑数

```C++
#include<iostream>
#include<vector>
using namespace std;
#define __tmain main
bool IsUglyNum(int num) {
	while (num % 5 == 0)
		num /= 5;
	while (num % 3 == 0)
		num /= 3;
	while (num % 2 == 0)
		num /= 2;
	return (num == 1);
}
int GetUglyNumber_Solution(int index) {
	if (index < 1)
		return 0;
	int num = 0, count = 0;
	while (count < index) {
		num++;
		if (IsUglyNum(num))
			count++;
	}
	return num;
}
int __tmain() {
	int n,result;
	cin >> n;
	result = GetUglyNumber_Solution(n);
	cout << result << endl;
}
```
---

### 空间换时间法：时间效率较高

根据丑数的定义，丑数应该是另一个丑数乘以2、3或者5的结果（1除外）。 因此我们可以创建一个数组，里面的数字是排好序的丑数。里面的每一个丑数是前面的丑数乘以2、3或者5得到的。那关键就是确保数组里的丑数是有序的了。 
我们假设数组中已经有若干个丑数，排好序后存在数组中。我们把现有的最大丑数记做M。 现在我们来生成下一个丑数，该丑数肯定是前面某一个丑数乘以2、3或者5的结果。 
我们首先考虑把已有的每个丑数乘以2。在乘以2的时候，能得到若干个结果小于或等于M的。由于我们是按照顺序生成的，小于或者等于M肯定已经在数组中了，我们不需再次考虑； 我们还会得到若干个大于M的结果，但我们只需要第一个大于M的结果，因为我们希望丑数是按从小到大顺序生成的，其他更大的结果我们以后再说。 
我们把得到的第一个乘以2后大于M的结果，记为M2。同样我们把已有的每一个丑数乘以3和5，能得到第一个大于M的结果M3和M5。那么下一个丑数应该是M2、M3和M5三个数的最小者。

```C++
class Solution {
public:
    int ugly[10000];
    int min(int a, int b, int c){
        int tmp = (a < b ? a : b);
        return (tmp < c ? tmp: c);
    }
    int GetUglyNumber_Solution(int N){
        ugly[0] = 1;
        int index2 = 0, index3 = 0,index5 = 0;
        int index = 0;
        while(index < N){
            int val = min(ugly[index2] * 2,
                         ugly[index3] * 3,
                         ugly[index5] * 5);
            if(val == ugly[index2] * 2)
                index2++;
            if(val == ugly[index3] * 3)
                index3++;
            if(val == ugly[index5] * 5)
                index5++;
            ugly[++index] = val;
        }
        int res = ugly[N-1];
        return res;
    }
};
```
