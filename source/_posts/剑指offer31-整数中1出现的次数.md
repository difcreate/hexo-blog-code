---
title: 剑指offer31-整数中1出现的次数
date: 2019-05-27 10:35:26
tags: [剑指offer,C++]
categories: [剑指offer,分治法]
---

# 整数中1出现的次数


---

## 题目描述

求出1~13的整数中1出现的次数,并算出100~1300的整数中1出现的次数？为此他特别数了一下1~13中包含1的数字有1、10、11、12、13因此共出现6次,但是对于后面问题他就没辙了。ACMer希望你们帮帮他,并把问题更加普遍化,可以很快的求出任意非负整数区间中1出现的次数（从1 到 n 中1出现的次数）。

---


## 解题思路

### 暴力破解法

最简单直接的方法就是我们循环所有的1~n中的每个number，计算每个number出现的次数
此方法简单，容易理解，但它的问题是效率，时间复杂度为$O(N * logN)$，N比较大的时候，需要耗费很长的时间。
```C++
class Solution {
public:
    int NumberOf1Between1AndN_Solution(int n)
    {
        int count = 0;

        for(int i = 1;
            i <= n;
            i++)
        {
            count += NumberOf1(i);
        }

        return count;
    }
    int NumberOf1(int number)
    {
        int count = 0;
        while(number != 0)
        {
            if(number % 10 == 1)
            {
                count++;
            }
            number /= 10;
        }

        return count;
    }
};
```
---

### 分治法
我们重新分析下这个问题，

对于任意一个个位数n，只要n>=1,它就包含一个"1"；

n<1，即n=0时，则包含的"1"的个数为0。

于是我们考虑用分治的思想将任意一个n位数不断缩小规模分解成许多个个位数，这样求解就很方便。

但是，我们该如何降低规模？

仔细分析，我们会发现，

**任意一个n位数中"1"的个位可以分解为两个n-1位数中"1"的个数的和，最后再加上一个与最高位数相关的常数C**
例如，

> 对于n=12，可以拆分为01-09,10-12，即 f(12) = f(10 - 1) + f(12 - 10) + 3,其中3是表示最高位为1的数字个数，这里就是10,11,12；
>
> 对于n=132，可以拆分为0-99，100-132，即f(132)=f(100 -1) + f(132 - 100) + 33，33代表最高位为1的数字的个数，这里就是100~132百位数字的1出新了33次
>
> 对于232，可以拆分为0-99，100-232，即f(232) = 2*f(100 - 1) + f(32) + 100，因为232大于199，所以它包括了所有最高位为1的数字即100~199，共100个。

综上，我们分析得出，最后加的常数C只跟最高位n1是否为1有关

- 当最高位为1时，常数C为原数字N去掉最高位后剩下的数字+1，如N=12时，$C = 2 + 1 = 3$，N=132时，$C = 32 + 1 = 33$

- 当最高位大于1时，常数C为$10^(bit-1)$，其中bit为N的位数，如N=232时，bit=3，$C = 10^(bit-1) = 10^2 = 100$。 于是，我们可以列出递归方程如下：
```
if(n1 == 1)
    f(n) = f(10bit-1) + f(n - 10bit) + n - 10bit+ 1;
else
    f(n) = n1*f(10bit-1) + f(n – n1*10bit) + 10bit;
```
进一步可以归结为
```
f(n) = n1*f(10bit-1) + f(n – n1*10bit) + LEFT;
其中
if(n1 == 1)
    LEFT = n - 10bit+ 1;
else
    LEFT = 10bit;
```
此算法的优点是不用遍历1~N就可以得到f(N)。经过我测试，此算法的运算速度比解法一快了许多许多，数字在1010内时，算法都可以在毫秒级内结束。
```C++
class Solution {
public:
    int NumberOf1Between1AndN_Solution(int n)
    {
        return CountOne(n);
    }
    int CountOne(int n){
        long count = 0;
        if(n < 1)
            count = 0;
        else if(n > 1 && n < 10)
            count = 1;
        else{// 计算n的位数
            long highest = n;//表示最高位的数字
            int bit = 0;
            while(highest >= 10){
                highest /= 10;
                bit++;
            }//  循环结束时, bit表示n的位数, 而highest是其最高位的数字
            int weight = pow(10, bit);//代表最高位的权重，即最高位一个1代表的大小
            if(highest == 1){
                count = CountOne(weight - 1)
                + CountOne(n - weight)
                + n - weight + 1;
            }
            else{
                count = highest * CountOne(weight - 1)
                + CountOne(n - highest * weight)
                + weight;
            }
        }
        return count;
    }
};
```