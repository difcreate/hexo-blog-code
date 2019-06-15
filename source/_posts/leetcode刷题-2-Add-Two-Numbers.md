---
title: leetcode刷题-2. Add Two Numbers
date: 2018-12-01 23:32:12
tags: [leetcode,链表]
categories: [leetcode,链表]
---

# 2. Add Two Numbers-两数相加

---

给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

示例：
```
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```

---

**思路：**

创建一个临时的头结点，并将指针cur指向该节点，之后遍历L1 和 L2两个链表，将他们对应位置节点的值相加，同和，如果该位置前的两个节点之和大于等于10，需要进位，则该节点值加1，然后对10取余，并新建节点保存，将cur的next指针指向该节点。

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
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* head = new ListNode(-1);
        ListNode* cur = head;
        int sum = 0;
        bool carry = false; 
        while(l1 != NULL || l2 != NULL){
            sum = 0;
            if(l1 != NULL){
                sum += l1->val;
                l1 = l1 -> next;
            }
            if(l2 != NULL){
                sum += l2->val;
                l2 = l2 -> next;
            }
            if(carry)
                sum++;
            cur -> next = new ListNode(sum % 10);
            cur = cur -> next;
            carry = sum >= 10 ? true: false;

        }
        if(carry)
            cur -> next = new ListNode(1);
        return head -> next;
    }
};
```