---
title: 剑指offer32-把数组排成最小的数
date: 2019-05-27 11:07:20
tags: [剑指offer,C++]
categories: [剑指offer,字符串]
---

# 把数组排成最小的数


---

## 题目描述

输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。

---


## 解题思路

对vector容器内的数据进行排序，按照将a和b转为string后.若 a＋b<b+a  a排在在前 的规则排序,如 2 21 因为 212 < 221 所以 排序后为 21 2 ,to_string() 可以将int 转化为string

Tips:
>sort中的比较函数compare要声明为静态成员函数或全局函数，不能作为普通成员函数，否则会报错 。
因为：非静态成员函数是依赖于具体对象的，而std::sort这类函数是全局的，因此无法再sort中调用非静态成员函数。
静态成员函数或者全局函数是不依赖于具体对象的, 可以独立访问，无须创建任何对象实例就可以访问。
同时静态/全局函数 不可以调用类的非静态成员。
sort 是将数组里所有的数都按照这个规则排序了, 排序完成以后, 数组里面数的排列就已经是最小的数了, 
再用一个循环拼接成字符串就好了
---

```C++
using namespace std;
class Solution {
public:
    static bool com(int a, int b){//定义一个比较函数，作为参数传入sort函数中
        string str1 = to_string(a);
        string str2 = to_string(b);
        return (str1+str2) < (str2+str1);
    }
    string PrintMinNumber(vector<int> numbers){
        string res = "";
        if(numbers.empty()) return res;
        sort(numbers.begin(), numbers.end(), com);
        for(int i = 0; i < numbers.size(); i++){
            res += to_string(numbers[i]);
        }
        return res;
    }
};
```