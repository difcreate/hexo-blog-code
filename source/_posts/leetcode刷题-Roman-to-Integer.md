---
title: leetcode刷题-13.Roman to Integer
date: 2018-11-20 15:08:44
tags: [leetcode,字符串]
categories: [leetcode,字符串]
---

# 13. Roman to Integer-罗马数字转整数

---

## 描述：

罗马数字包含以下七种字符: I， V， X， L，C，D 和 M。
```
字符          数值
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```
例如， 罗马数字 2 写做 II ，即为两个并列的 1。12 写做 XII ，即为 X + II 。 27 写做  XXVII, 即为 XX + V + II 。

通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下六种情况：

- I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
- X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。 
- C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。

给定一个罗马数字，将其转换成整数。输入确保在 1 到 3999 的范围内。

示例 1:
```
输入: "III"
输出: 3
```
示例 2:
```
输入: "IV"
输出: 4
```
示例 3:
```
输入: "IX"
输出: 9
```
示例 4:
```
输入: "LVIII"
输出: 58
解释: L = 50, V= 5, III = 3.
```
示例 5:
```
输入: "MCMXCIV"
输出: 1994
解释: M = 1000, CM = 900, XC = 90, IV = 4.
```

---

## 思路：

从前往后遍历，用一个临时变量记录分段数字。如果当前数字比上一个数字大，说明这一段的值应该用当前这个值减去上一个值，比如**IV = 5 - 1**；否则，将当前值加入到结果中，开始下一段记录。比如：**VI = 5 + 1,II = 1 + 1**;

>代码：
```c++
 class Solution {
public:
    inline int map(char c){
        switch(c){
        case 'I': return 1;
        case 'V': return 5;
        case 'X': return 10;
        case 'L': return 50;
        case 'C': return 100;
        case 'D': return 500;
        case 'M': return 1000;
        default: return 0;
        }
    }
    
    int romanToInt(string s) {
        int result = 0;
        for(int i = 0; i < s.size(); ++i){
            if(i > 0 && (map(s[i]) > map(s[i-1]))){
                //这里减去两倍的上一个值是前一个值在迭代时已经加过了，所以要减两次
                result += (map(s[i]) - 2 * map(s[i-1]));
            }else
               result += map(s[i]);
        }
    return result;
    }
};
```
