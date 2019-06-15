---
title: leetcode刷题-141. Linked List Cycle
date: 2018-11-21 23:47:59
tags: [leetcode,链表]
categories: [leetcode,链表]
---

# 141. Linked List Cycle-环形链表

---

## 描述：

给定一个链表，判断链表中是否有环。

**思路：**
最好的方法是时间复杂度O(n),空间复杂度O(1)。设置两个指针，一个快，一个慢，快的指针每次走两步，慢的指针每次走一步，如果两个指针相遇，则说明有环。

代码：

```c++
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
 //时间复杂度O(n),空间复杂度O(1)
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode *slow = head,*fast = head;
        while(fast && fast->next){
            slow = slow->next;
            fast = fast->next->next;
            if(slow == fast) return true;
        }
        return false;
    }
};
```