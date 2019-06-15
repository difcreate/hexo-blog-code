---
title: 剑指offer36-两个链表的第一个公共结点
date: 2019-05-30 22:39:10
tags: [剑指offer,C++]
categories: [剑指offer,链表]
---

# 两个链表的第一个公共结点


---

## 题目描述

输入两个链表，找出它们的第一个公共结点。

---


## 解题思路

### 暴力破解法

最简单直接的方法就是，对于第一个链表的每个节点，我们依次判断其是不是第二条链表的公共结点

```C++
/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {
public:
    ListNode* FindFirstCommonNode( ListNode* pHead1, ListNode* pHead2) {
        ListNode *p1 = nullptr;
        ListNode *p2 = nullptr;
        //  循环第一个链表的每个结点
        for(p1 = pHead1; p1 != nullptr; p1 = p1->next){
             //  依次判断其在不在第二条链表中
            for(p2 = pHead2; p2 != nullptr; p2 = p2->next){
                if(p1 == p2)
                    break;
            }
            // 判断两个链表没有公共节点的请况就返回nullptr
            if(p1 == p2)
                break;
        }
        return p1;
    }
};
```
---

### 右对齐两个链表

如果两个链表有公共节点，则它们的形状必然是一个Y字形。

**长链表先走，实现右对齐**

先假设这两个链表的长度相等，则我们可以同步遍历这两个链表，找到公共节点。现在有两个链表，我们可以先分别求齐长度得其差n，然后遍历长的那个链表n个节点，然后同步遍历这两个链表即可。

```C++
/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {
public:
    ListNode* FindFirstCommonNode( ListNode* pHead1, ListNode* pHead2) {
        // 使得两个链表右对齐
        ListNode *p1 = pHead1;
        ListNode *p2 = pHead2;
        if(p1 == NULL || p2 == NULL)
            return NULL;
        int len1 = 0, len2 = 0;
        while(p1 != NULL){
            p1 = p1->next;
            len1++;
        }
        while(p2 != NULL){
            p2 = p2->next;
            len2++;
        }
        p1 = pHead1; 
        p2 = pHead2;
        if(len1 > len2){
             int sublen1 = len1 - len2;
             while(sublen1 > 0){
                p1 = p1->next;
                 sublen1--;
            }
        }
        else{
            int sublen2 = len2 - len1;
             while(sublen2 > 0){
                p2 = p2->next;
                 sublen2--;
            }
        }
        while(p1 != NULL && p2 != NULL){
            if(p1 == p2){
                break;
            }
            p1 = p1->next;
            p2 = p2->next;
        }
        return p1;
    }
}
```