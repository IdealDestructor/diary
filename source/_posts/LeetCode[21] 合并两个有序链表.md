---
title: LeetCode[21] 合并两个有序链表
date: 2021-03-28 11:29:45
tags: [C++,LeetCode,递归,链表]
categories: 编程算法
widgets: null
---

### Related Topics:

"递归": https://leetcode.com/tag/recursion/ "链表": https://leetcode.com/tag/linked-list/ Similar Questions: "合并K个升序链表": https://leetcode.com/problems/merge-k-sorted-lists/ "合并两个有序数组": https://leetcode.com/problems/merge-sorted-array/ "排序链表": https://leetcode.com/problems/sort-list/ "最短单词距离 II": https://leetcode.com/problems/shortest-word-distance-ii/

### Problem:

将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

**示例 1：**

```
输入：l1 = [1,2,4], l2 = [1,3,4]
输出：[1,1,2,3,4,4]
```

**示例 2：**

```
输入：l1 = [], l2 = []
输出：[]
```

**示例 3：**

```
输入：l1 = [], l2 = [0]
输出：[0]
```

**提示：**

- 两个链表的节点数目范围是 `[0, 50]`
- `100 <= Node.val <= 100`
- `l1` 和 `l2` 均按 **非递减顺序** 排列

<!--more-->

### Solution:

https://leetcode-cn.com/problems/merge-two-sorted-lists/solution/dong-hua-yan-shi-21-he-bing-liang-ge-you-blxu/

```cpp
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
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if (l1 == nullptr) {
            return l2;
        } else if (l2 == nullptr) {
            return l1;
        } else if (l1->val < l2->val) {
            l1->next = mergeTwoLists(l1->next, l2);
            return l1;
        } else {
            l2->next = mergeTwoLists(l1, l2->next);
            return l2;
        }
    }
};
```