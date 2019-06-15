---
title: 剑指offer29-最小的K个数
date: 2019-05-24 11:01:36
tags: [剑指offer,C++]
categories: [剑指offer,堆排序，冒泡排序]
---

# 最小的K个数


---

## 题目描述

输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4,。

---


## 解题思路

采用冒泡排序法, K趟找出前K个数字

---

```C++
vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
    vector<int> res;
    int len = input.size();
    if(len < k)
        return res;
    
    for(int i = 0; i < k; i++){ //冒泡排序前K个元素
        for(int j = len - 1; j > 0; j--){
            if(input[j] < input[j - 1])
                swap(input[j], input[j - 1]);
        }
        res.push_back(input[i]);
    }
    return res;
}
```
