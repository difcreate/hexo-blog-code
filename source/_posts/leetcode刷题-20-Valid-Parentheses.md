---
title: leetcode刷题-20. Valid Parentheses
date: 2018-11-29 19:38:19
tags: [leetcode,栈]
categories: [leetcode,栈]
---

# 20. Valid Parentheses-有效的括号

---

## 描述：

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。
注意空字符串可被认为是有效字符串。

示例 1:
```
输入: "()"
输出: true
```
示例 2:
```
输入: "()[]{}"
输出: true
```
示例 3:
```
输入: "(]"
输出: false
```
示例 4:
```
输入: "([)]"
输出: false
```
示例 5:
```
输入: "{[]}"
输出: true
```

---

**思路：**

利用**栈stack数据结构**来解决问题；首先判断字符串是否为空，若为空则返回true；然后判断只要栈中的元素为空，就入栈，而且栈顶和目前的元素无法配对的时候，同样打入栈中，这样避免了一开始就是右括号，不入栈；若栈顶是对应的左括号接下来要访问栈顶的元素，最后只需要判断栈中是不是为空就好了。

---

```c++
class Solution {
public:
    bool isValid(string s) {
        stack<char> result;  
        int n=s.size();  
        if(n==0) return true;  
        for(int i=0;i<n;i++)  {  
            if(result.empty())  
                result.push(s[i]);  
            else if(result.top()=='('&&s[i]==')'||  
                  result.top()=='['&&s[i]==']'||  
                  result.top()=='{'&&s[i]=='}')   
                    result.pop();  
            else  
                result.push(s[i]);  

        }  
        return result.empty(); 
    }
};
```