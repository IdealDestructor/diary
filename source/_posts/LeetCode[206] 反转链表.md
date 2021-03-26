---
title: LeetCode[206] 反转链表
date: 2021-03-25 11:02:35
tags: [C++,LeetCode]
categories: 编程算法
---
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
    //迭代
    //     ListNode *prev = nullptr;
    //     ListNode *curr = head;
    //     while (curr) {
    //         ListNode *tempnext = curr->next;
    //         curr->next = prev;
    //         prev = curr;
    //         curr = tempnext;
    //     }
    //     return prev;
    // }
    //递归
        if(!head || !head->next) {
            return head;
        }
        ListNode* newHead = reverseList(head->next);
        head->next->next = head;
        head->next = nullptr;
        return newHead;
    }
};
```

