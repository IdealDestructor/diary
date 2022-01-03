---
title: LeetCode[104] 二叉树的最大深度
date: 2022-01-03 11:29:45
tags: [C++,LeetCode,二叉树]
categories: 编程算法
widgets: null
---

---
Related Topics:
  "树": https://leetcode.com/tag/tree/
  "深度优先搜索": https://leetcode.com/tag/depth-first-search/
  "广度优先搜索": https://leetcode.com/tag/breadth-first-search/
  "二叉树": https://leetcode.com/tag/binary-tree/
Similar Questions:
  "平衡二叉树": https://leetcode.com/problems/balanced-binary-tree/
  "二叉树的最小深度": https://leetcode.com/problems/minimum-depth-of-binary-tree/
  "N 叉树的最大深度": https://leetcode.com/problems/maximum-depth-of-n-ary-tree/

### Problem:

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

**说明:** 叶子节点是指没有子节点的节点。

**示例：**
给定二叉树 `[3,9,20,null,null,15,7]`，

```
3
   / \
  9  20
    /  \
   15   7
```

返回它的最大深度 3 。

<!--more-->

### Solution:

https://programmercarl.com/0104.二叉树的最大深度.html#递归法

```c++
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(root == nullptr) return 0;
        int leftDepth = maxDepth(root->left);
        int rightDepth = maxDepth(root->right);
        return max(leftDepth, rightDepth) + 1;
    }
};
```

