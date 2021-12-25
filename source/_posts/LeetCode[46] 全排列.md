---
title: LeetCode[46] 全排列
date: 2021-10-06 21:03:35
tags: [C++,LeetCode,回溯]
categories: 编程算法
widgets: null
---

Related Topics:
  "数组": https://leetcode.com/tag/array/
  "回溯": https://leetcode.com/tag/backtracking/
Similar Questions:
  "下一个排列": https://leetcode.com/problems/next-permutation/
  "全排列 II": https://leetcode.com/problems/permutations-ii/
  "排列序列": https://leetcode.com/problems/permutation-sequence/

  "组合": https://leetcode.com/problems/combinations/
---

### Problem:

给定一个不含重复数字的数组 `nums` ，返回其 **所有可能的全排列** 。你可以 **按任意顺序** 返回答案。

**示例 1：**

```
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

**示例 2：**

```
输入：nums = [0,1]
输出：[[0,1],[1,0]]
```

**示例 3：**

```
输入：nums = [1]
输出：[[1]]
```

**提示：**

- `1 <= nums.length <= 6`
- `-10 <= nums[i] <= 10`
- `nums` 中的所有整数 **互不相同**

<!--more-->

### Solution:

回溯法

![截屏2021-10-06 下午4.41.30](https://i.loli.net/2021/10/06/jnuBkRLlzZeCvo8.png)

```c++
class Solution {
public:
    void backtrack(vector<vector<int>>& res, vector<int>& output, int first, int len){
        // 所有数都填完了
        if (first == len) {
            res.emplace_back(output);
            return;
        }
        for (int i = first; i < len; ++i) {
            // 动态维护数组
            swap(output[i], output[first]);
            // 继续递归填下一个数
            backtrack(res, output, first + 1, len);
            // 撤销操作
            swap(output[i], output[first]);
        }
    }
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int> > res;
        backtrack(res, nums, 0, (int)nums.size());
        return res;
    }
};
```

