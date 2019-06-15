---
title: 剑指offer28-数组中出现次数超过一半的数字
date: 2019-05-24 10:26:58
tags: [剑指offer,C++]
categories: [剑指offer,二叉树]
---

# 数组中出现次数超过一半的数字


---

**题目描述**

数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。

---


**C++解题思路**

**充分利用出现次数超过一半这个条件**

数组中有一个数字出现的次数超过数组长度的一半，也就是说它出现的次数比其他所有数字出现的次数之和还要多

我们考虑**阵地攻守**（镇守阵地），遇见一个友军就抱成团，遇见一个敌军就同归于尽，那么最后战场上剩余的肯定就是人数（出现次数）最多的那个队伍（数字）

采用阵地攻守的思想：

- 第一个数字作为第一个士兵，守阵地；count = 1；

- 遇到相同元素，count++; 遇到不相同元素，即为敌人，同归于尽,count--；

- 当遇到count为0的情况，又以新的i值作为守阵地的士兵，继续下去，到最后还留在阵地上的士兵，有可能是主元素。

- 再加一次循环，记录这个士兵的个数看是否大于数组一般即可。

> 由于我们要找的数字出现的次数比他所有数字出现的次数之和还要多，那么要找的数字肯定是最后一次把次数设为1时对应的数字，因为少的元素都已经阵亡了，战场上仅剩下的一定是人数数目要多的那个队伍

---

```C++
class Solution {
public:
    int MoreThanHalfNum_Solution(vector<int> numbers) {
        if(numbers.size() == 0)
            return 0;
        else if(numbers.size() == 1)
            return numbers[0];
        int len = numbers.size(), key = numbers[0], count = 1;
        for(int i = 1; i < len; i++){
            if(key == numbers[i]){
                count++;
            }
            else{
                count--;
                if(count < 1){
                    key = numbers[i];
                }
            }
        }
        count = 0;
        for(int j = 0; j < len; j++){
            if(key == numbers[j])
                count++;
        }
        if(count > (len / 2))
            return key;
        return 0;
    }
};
```