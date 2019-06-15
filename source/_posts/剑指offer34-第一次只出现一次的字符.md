---
title: 剑指offer34-第一次只出现一次的字符
date: 2019-05-29 20:49:42
tags: [剑指offer,C++]
categories: [剑指offer,字符串]
---

# 第一次只出现一次的字符


---

## 题目描述

在一个字符串(0<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）.

---


## 解题思路

使用辅助数组进行计数，统计每个字符串的出现的次数，然后查找第一个只出现一次的字符位置

---

```C++
class Solution {
public:
    int FirstNotRepeatingChar(string str) {
        if(str.length() == 0)
            return -1;
        int count[256];
        //  将计数器数组清0
        memset(count, '\0', sizeof(count));
        //  对字符串中出现的每个字符进行计数
        for(int i = 0; i < str.length(); i++){
            count[(int) str[i]]++;
        }
        //  第一个只出现一次的字符
        for(int j = 0; j < str.length(); j++){
            if(count[str[j]] == 1)
                return j;
        }
        return -1;
    }
};
```