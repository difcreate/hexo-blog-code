---
title: 剑指offer21-栈的压入弹出序列
date: 2019-05-22 11:10:55
tags: [剑指offer,C++]
categories: [剑指offer,栈]
---
# 栈的压入弹出序列


---

**题目描述**

输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

---


**C++解题思路**

开辟一个辅助栈，模拟入栈出栈过程(假设pushV为入栈序列，popV为出栈序列)

pushV中的元素依次压入辅助栈s,push++；设置变量push,pop分别代表pushV和popV当前元素的位置；

新压入的元素与弹出序列的pop位元素相同，辅助栈弹出，同时pop++

不相同，pushV中的元素继续入辅助栈s，push++；

- 如果下一个弹出的数字刚好是栈顶数字，则直接弹出。

- 若下一个弹出的数字不在栈顶，则把压栈序列中还没有入栈的数字压入辅助栈，直到把下一个需要弹出的数字压入栈顶为止。

- 若所有的数字都压入栈了仍没有找到下一个弹出的数字，则表明该序列不可能滴一个弹出序列。
```C++
class Solution {
public:
    bool IsPopOrder(vector<int> pushV,vector<int> popV) {
        if(pushV.size() == 0 && popV.size() == 0)
            return false;
        else if(pushV.size() != popV.size())
            return false;
        stack<int> s;
        s.push(pushV[0]);
        for(int push = 0, pop = 0; push < pushV.size() && pop < popV.size();){
            if(s.empty() != true && s.top() == popV[pop]){
                s.pop();
                pop++;
            }
            else{
                s.push(pushV[++push]);
            }
        }
        if(s.empty())
            return true;
        else
            return false;
    }
};
```