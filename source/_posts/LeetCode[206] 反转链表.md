---
title: LeetCode[206] 反转链表
date: 2021-03-25 11:02:35
tags: [C++,LeetCode]
categories: 编程算法
widgets: null
---

方法一：迭代

在遍历链表时，将当前节点的 next 指针改为指向前一个节点。由于节点没有引用其前一个节点，因此必须事先存储其前一个节点。在更改引用之前，还需要存储后一个节点。最后返回新的头引用。

复杂度分析

时间复杂度：O(n)，其中 n 是链表的长度。需要遍历链表一次。空间复杂度：O(1)。

方法二：递归

递归版本稍微复杂一些，其关键在于反向工作。假设链表的其余部分已经被反转，现在应该如何反转它前面的部分？

<!--more-->

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

