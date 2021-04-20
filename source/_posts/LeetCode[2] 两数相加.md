---
title: LeetCode[2] 两数相加
date: 2021-03-01 16:29:45
tags: [C++,LeetCode]
categories: 编程算法
---

### Related Topics:

"递归": https://leetcode.com/tag/recursion/ "链表": https://leetcode.com/tag/linked-list/ "数学": https://leetcode.com/tag/math/ Similar Questions: "字符串相乘": https://leetcode.com/problems/multiply-strings/ "二进制求和": https://leetcode.com/problems/add-binary/ "两整数之和": https://leetcode.com/problems/sum-of-two-integers/ "字符串相加": https://leetcode.com/problems/add-strings/ "两数相加 II": https://leetcode.com/problems/add-two-numbers-ii/ "数组形式的整数加法": https://leetcode.com/problems/add-to-array-form-of-integer/

### Problem:

给你两个 **非空** 的链表，表示两个非负的整数。它们每位数字都是按照 **逆序** 的方式存储的，并且每个节点只能存储 **一位** 数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

**示例 1：**

```
输入：l1 = [2,4,3], l2 = [5,6,4]
输出：[7,0,8]
解释：342 + 465 = 807.
```

**示例 2：**

```
输入：l1 = [0], l2 = [0]
输出：[0]
```

**示例 3：**

```
输入：l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
输出：[8,9,9,9,0,0,0,1]
```

**提示：**

- 每个链表中的节点数在范围 `[1, 100]` 内
- `0 <= Node.val <= 9`
- 题目数据保证列表表示的数字不含前导零

### Solution:

https://leetcode-cn.com/problems/add-two-numbers/solution/zhi-zhen-lian-biao-jian-dan-yi-li-jie-on-by-guai-m/

```cpp
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
        ListNode* root = new ListNode(0);
        ListNode* cursor = root;
        int carry = 0;
        while(l1 != NULL || l2 != NULL || carry != 0){
            int l1Val = l1!= NULL ? l1->val : 0;
            int l2Val = l2!= NULL ? l2->val : 0;
            int sumVal = l1Val + l2Val + carry;
            carry = sumVal / 10;

            ListNode* sumNode = new ListNode(sumVal % 10);
            cursor->next = sumNode;
            cursor = sumNode;

            if(l1 != NULL) l1 = l1->next;
            if(l2 != NULL) l2 = l2->next;
        } 
        return root->next;
    }
};
```