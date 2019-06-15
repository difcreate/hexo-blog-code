---
title: leetcode刷题
date: 2019-06-05 11:23:33
tags: [leetcode,链表]
categories: [leetcode,链表]
---

# 92-反转链表||

## 题目描述
反转从位置 m 到 n 的链表。请使用一趟扫描完成反转。

说明:
1 ≤ m ≤ n ≤ 链表长度。

示例:
```
输入: 1->2->3->4->5->NULL, m = 2, n = 4
输出: 1->4->3->2->5->NULL
```
## 解题思路

创建一个头结点方便后续操作，首先遍历m-1次到即将反转的前一个结点；
然后进行局部链表反转；
**思路一：**
```c++
ListNode* pnode = pre->next;
for(int i = m; i < n; i++){
    ListNode* pnext = pnode->next;
    pnode->next = pnext->next;
    pnext->next = pre->next;
    pre->next = pnext;
}
```
**思路二：**

```c++
ListNode* ppre = NULL;
ListNode* pnode = pre->next;
for(int i = m; i <= n; i++){
    ListNode* pnext = pnode->next;
    pnode->next = ppre;
    ppre = pnode;
    pnode = pnext;
}
pre->next->next = pnode;
pre->next = ppre;
```

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        ListNode* L = new ListNode(-1);
        L->next = head;
        ListNode* pre = L;
        for(int i = 1; i < m; i++){
            pre = pre->next;
        }
        //ListNode* ppre = NULL;
        ListNode* pnode = pre->next;
        for(int i = m; i < n; i++){
            ListNode* pnext = pnode->next;
            pnode->next = pnext->next;
            pnext->next = pre->next;
            pre->next = pnext;
        }
        /*
        for(int i = m; i <= n; i++){
            ListNode* pnext = pnode->next;
            pnode->next = ppre;
            ppre = pnode;
            pnode = pnext;
        }
        
        pre->next->next = pnode;
        pre->next = ppre;
        */
        return L->next;
    }
};
```
---
