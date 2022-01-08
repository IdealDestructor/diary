---
title: LeetCode[110] 平衡二叉树
date: 2022-01-06 11:29:45
tags: [C++,LeetCode,二叉树]
categories: 编程算法
widgets: null
---

---
Related Topics:
  "树": https://leetcode.com/tag/tree/
  "深度优先搜索": https://leetcode.com/tag/depth-first-search/
  "二叉树": https://leetcode.com/tag/binary-tree/
Similar Questions:
   "二叉树的最大深度": https://leetcode.com/problems/maximum-depth-of-binary-tree/

### Problem:

给定一个二叉树，判断它是否是高度平衡的二叉树。

本题中，一棵高度平衡二叉树定义为：

一个二叉树*每个节点*的左右两个子树的高度差的绝对值不超过 1 。

**示例 1：**

```
输入：root = [3,9,20,null,null,15,7]
输出：true
```

**示例 2：**

```
输入：root = [1,2,2,3,3,null,null,4,4]
输出：false
```

**示例 3：**

```
输入：root = []
输出：true
```

**提示：**

- 树中的节点数在范围 `[0, 5000]` 内
- `-104 <= Node.val <= 104`

<!--more-->

### Solution:

https://programmercarl.com/0110.平衡二叉树.html#递归

```c++
class Solution {
public:
    int getHeight(TreeNode * node){
        if(node == NULL) return 0;
        int left = getHeight(node->left);
        if (left == -1) return -1;
        int right = getHeight(node->right);
        if (right == -1) return -1;
        if (abs(left - right) > 1) return -1;
        return max(left, right) + 1;
    }
    bool isBalanced(TreeNode* root) {
        return getHeight(root) == -1 ? false : true;
    }
};
```

