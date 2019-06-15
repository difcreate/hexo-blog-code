---
title: 剑指offer20-包含min函数的栈
date: 2019-05-22 10:52:44
tags: [剑指offer,C++]
categories: [剑指offer,栈]
---

# 包含min函数的栈


---

**题目描述**

定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））

---


**C++解题思路**

我们用一个辅助栈来实现最小值的更新工作。

这个辅助栈工作原理：
- 入栈时：
    - 1）当数据栈为空时，进入栈的元素同时也进入辅助栈；
    - 2）当它不为空时，就把该入栈元素与辅助栈的栈顶元素进行比较，若入栈元素小，则该元素同时也进入辅助栈；若不是，则对辅助栈不进行操作

- 出栈时：
    - 1）当时辅助栈的栈顶元素等于处理数据的数据栈栈顶元素时，不经数据栈要弹出元素，辅助栈也要弹出栈顶元素，
    - 2)当不等时，只对数据栈进行出栈操作。

这样我们思路就很明确了：**min函数只需返回辅助栈的栈顶源。**
```C++
class Solution {
public:
    void push(int value)
    {
        datastack.push(value);
        //辅助栈为空或者即将入栈的值小于辅助栈栈顶元素
        if(minstack.empty() || value < minstack.top())
            minstack.push(value);
    }

    void pop()
    {
        if(datastack.empty())//数据栈为空
            return ;
        if(datastack.top() == minstack.top()) //数据栈和辅助栈栈顶元素相同
            minstack.pop();
        datastack.pop();
    }

    int top()
    {
        return datastack.top();
    }

    int min()
    {
        return minstack.top();
    }
private:
    stack<int>  datastack;     //  数据栈
    stack<int>  minstack;      //  存储每次栈中最小值的栈信息
};
```



