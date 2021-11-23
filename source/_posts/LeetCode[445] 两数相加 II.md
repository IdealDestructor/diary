---
title: LeetCode[445] 两数相加 II
date: 2021-11-23 12:02:35
tags: [C++,LeetCode]
categories: 编程算法
widgets: null
---

---
Related Topics:
  "栈": https://leetcode.com/tag/stack/
  "链表": https://leetcode.com/tag/linked-list/
  "数学": https://leetcode.com/tag/math/
Similar Questions:
"两数相加": https://leetcode.com/problems/add-two-numbers/

### Problem:

给你两个 **非空**链表来代表两个非负整数。数字最高位位于链表开始位置。它们的每个节点只存储一位数字。将这两数相加会返回一个新的链表。

你可以假设除了数字 0 之外，这两个数字都不会以零开头。

**示例1：**

```
输入：l1 = [7,2,4,3], l2 = [5,6,4]
输出：[7,8,0,7]
```

**示例2：**

```
输入：l1 = [2,4,3], l2 = [5,6,4]
输出：[8,0,7]
```

**示例3：**

```
输入：l1 = [0], l2 = [0]
输出：[0]
```

**提示：**

- 链表的长度范围为`[1, 100]`
- `0 <= node.val <= 9`
- 输入数据保证链表代表的数字无前导 0

**进阶：**如果输入链表不能修改该如何处理？换句话说，不能对列表中的节点进行翻转。

### Solution:



*Template generated via [Leetmark](https://github.com/crimx/crx-leetmark).*

<!--more-->

### Solution:

```cpp
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        stack<int> s1,s2;
        while (l1)
        {
            s1.push(l1->val);
            l1 = l1->next;
        }
        while (l2)
        {
            s2.push(l2->val);
            l2 = l2->next;
        }
        int carry = 0;
        ListNode* ans = nullptr;
        while (!s1.empty() || !s2.empty() || carry){
            int s1tail = s1.empty() ? 0 : s1.top();
            if(!s1.empty()){
                s1.pop();
            }
            int s2tail = s2.empty() ? 0 : s2.top();
            if(!s2.empty()){
                s2.pop();
            }
            int sum = carry + s1tail + s2tail;
            carry = sum / 10;
            auto curNode = new ListNode(sum % 10);
            curNode->next = ans;
            ans = curNode;
        }
        return ans;
    }
};
```