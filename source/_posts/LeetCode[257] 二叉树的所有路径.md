---
title: LeetCode[257] 二叉树的所有路径
date: 2022-01-08 11:29:45
tags: [C++,LeetCode,二叉树]
categories: 编程算法
widgets: null
---

- Related Topics:
    "树": https://leetcode.com/tag/tree/
    "深度优先搜索": https://leetcode.com/tag/depth-first-search/
    "字符串": https://leetcode.com/tag/string/
    "二叉树": https://leetcode.com/tag/binary-tree/
  Similar Questions:
    "路径总和 II": https://leetcode.com/problems/path-sum-ii/
    "从叶结点开始的最小字符串": https://leetcode.com/problems/smallest-string-starting-from-leaf/
  
  ### Problem:
  
  给你一个二叉树的根节点 `root` ，按 **任意顺序** ，返回所有从根节点到叶子节点的路径。
  
  **叶子节点** 是指没有子节点的节点。
  
  **示例 1：**
  
  ```
  输入：root = [1,2,3,null,5]
  输出：["1->2->5","1->3"]
  ```
  
  **示例 2：**
  
  ```
  输入：root = [1]
  输出：["1"]
  ```
  
  **提示：**
  
  - 树中节点的数目在范围 `[1, 100]` 内
  - `-100 <= Node.val <= 100


<!--more-->

### Solution:

https://programmercarl.com/0257.二叉树的所有路径.html#递归

```c++
class Solution {
private:

    void traversal(TreeNode* cur, vector<int>& path, vector<string>& result) {
        path.push_back(cur->val);
        // 这才到了叶子节点
        if (cur->left == NULL && cur->right == NULL) {
            string sPath;
            for (int i = 0; i < path.size() - 1; i++) {
                sPath += to_string(path[i]);
                sPath += "->";
            }
            sPath += to_string(path[path.size() - 1]);
            result.push_back(sPath);
            return;
        }
        if (cur->left) {
            traversal(cur->left, path, result);
            path.pop_back(); // 回溯
        }
        if (cur->right) {
            traversal(cur->right, path, result);
            path.pop_back(); // 回溯
        }
    }

public:
    vector<string> binaryTreePaths(TreeNode* root) {
        vector<string> result;
        vector<int> path;
        if (root == NULL) return result;
        traversal(root, path, result);
        return result;
    }
};
```

