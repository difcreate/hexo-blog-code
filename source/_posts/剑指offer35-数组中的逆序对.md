---
title: 剑指offer35-数组中的逆序对
date: 2019-05-29 21:12:22
tags: [剑指offer,C++]
categories: [剑指offer,数组]
---

# 数组中的逆序对


---

## 题目描述

在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组,求出这个数组中的逆序对的总数P。并将P对1000000007取模的结果输出。 即输出P%1000000007

---


## 解题思路

### 暴力破解法

两个 for 循环枚举所有的数对，如果是逆序对，则 count++，最终返回 count 即可。时间复杂度$O(n^2)$，运行超时；

```C++
class Solution {
public:
    int InversePairs(vector<int> data) {
        if(data.empty()) return 0;
        int count = 0;
        for(int i = 0; i < data.size() - 1; i++){
            for(int j = i + 1; j < data.size(); j++){
                if(data[i] > data[j])
                    count++;
            }
        }
        count %= 1000000007;
        return count;
    }
};
```
---

### 排序--计算交换次数

> 考虑一下，逆序是说a[i]>a[j]，i<j。那么在排序的过程中，会把a[i]和a[j]交换过来，这个交换的过程，每交换一次，就是一个逆序对的“正序”过程。

**冒泡排序**
时间复杂度$O(n^2)$，运行超时；

```C++
class Solution {
public:
    int InversePairs(vector<int> data) {
        if(data.empty()) return 0;
        int count = 0;
        bool IsSwap = true;
        for(int i = 0; i < data.size() - 1; i++){
            IsSwap = false;
            for(int j = 0; j < data.size() - i - 1; j++){
                if(data[j] > data[j+1]){
                    swap(data[j], data[j+1]);
                    count++;
                    IsSwap = true;
                }
            }
            if(!IsSwap)
                break;
        }
        return count;
    }
};
```

**归并排序**
