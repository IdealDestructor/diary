---
title: LeetCode[102] 二叉树的层序遍历
date: 2021-12-28 11:29:45
tags: [C++,LeetCode,二叉树]
categories: 编程算法
widgets: null
---

Related Topics:
  "树": https://leetcode.com/tag/tree/
  "广度优先搜索": https://leetcode.com/tag/breadth-first-search/
  "二叉树": https://leetcode.com/tag/binary-tree/
Similar Questions:
  "二叉树的锯齿形层序遍历": https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/
  "二叉树的层序遍历 II": https://leetcode.com/problems/binary-tree-level-order-traversal-ii/
  "二叉树的最小深度": https://leetcode.com/problems/minimum-depth-of-binary-tree/
  "二叉树的垂直遍历": https://leetcode.com/problems/binary-tree-vertical-order-traversal/
  "二叉树的层平均值": https://leetcode.com/problems/average-of-levels-in-binary-tree/
  "N 叉树的层序遍历": https://leetcode.com/problems/n-ary-tree-level-order-traversal/
  "二叉树的堂兄弟节点": https://leetcode.com/problems/cousins-in-binary-tree/


### Problem:

给你一个二叉树，请你返回其按 **层序遍历** 得到的节点值。 （即逐层地，从左到右访问所有节点）。

**示例：**
二叉树：`[3,9,20,null,null,15,7]`,

```
3
   / \
  9  20
    /  \
   15   7
```

返回其层序遍历结果：

```
[
  [3],
  [9,20],
  [15,7]
]
```

<!--more-->

### Solution:

https://programmercarl.com/0102.二叉树的层序遍历.html

```c++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        queue<TreeNode*> que;
        if (root != NULL) que.push(root);
        vector<vector<int>> result;
        while (!que.empty()) {
            int size = que.size();
            vector<int> vec;
            // 这里一定要使用固定大小size，不要使用que.size()，因为que.size是不断变化的
            for (int i = 0; i < size; i++) {
                TreeNode* node = que.front();
                que.pop();
                vec.push_back(node->val);
                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
            result.push_back(vec);
        }
        return result;
    }
};
```

