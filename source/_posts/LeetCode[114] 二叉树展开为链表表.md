---
title: LeetCode[114] 二叉树展开为链表
date: 2021-12-05 17:05:45
tags: [C++,LeetCode]
categories: 编程算法
widgets: null
---

Related Topics:
  "栈": https://leetcode.com/tag/stack/
  "树": https://leetcode.com/tag/tree/
  "深度优先搜索": https://leetcode.com/tag/depth-first-search/
  "链表": https://leetcode.com/tag/linked-list/
  "二叉树": https://leetcode.com/tag/binary-tree/
Similar Questions:
 "扁平化多级双向链表": https://leetcode.com/problems/flatten-a-multilevel-doubly-linked-list/

### Problem:

给你二叉树的根结点 `root` ，请你将它展开为一个单链表：

- 展开后的单链表应该同样使用 `TreeNode` ，其中 `right` 子指针指向链表中下一个结点，而左子指针始终为 `null` 。
- 展开后的单链表应该与二叉树 [**先序遍历**](https://baike.baidu.com/item/%E5%85%88%E5%BA%8F%E9%81%8D%E5%8E%86/6442839?fr=aladdin) 顺序相同。

**示例 1：**

```
输入：root = [1,2,5,3,4,null,6]
输出：[1,null,2,null,3,null,4,null,5,null,6]
```

**示例 2：**

```
输入：root = []
输出：[]
```

**示例 3：**

```
输入：root = [0]
输出：[0]
```

**提示：**

- 树中结点数在范围 `[0, 2000]` 内
- `-100 <= Node.val <= 100`

**进阶：**你可以使用原地算法（`O(1)` 额外空间）展开这棵树吗？

<!--more-->

### Solution:

https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/solution/tong-su-yi-dong-de-si-lu-fen-xi-zong-gon-bi7h/

```c++
lass Solution {
public:
    void flatten(TreeNode* root) {
        if(root == nullptr){
            return;
        }
        flatten(root->left);
        flatten(root->right);

        TreeNode* l = root->left;
        TreeNode* r = root->right;

        root->left = nullptr;
        root->right = l;

        while(root->right != nullptr){
            root = root->right;
        }
        root->right = r;
    }
};
```

