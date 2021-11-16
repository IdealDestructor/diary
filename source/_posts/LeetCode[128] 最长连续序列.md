---
title: LeetCode[128] 最长连续序列
date: 2021-11-14 11:29:45
tags: [C++,LeetCode]
categories: 编程算法
---

- Related Topics:
    "并查集": https://leetcode.com/tag/union-find/
    "数组": https://leetcode.com/tag/array/
    "哈希表": https://leetcode.com/tag/hash-table/
  Similar Questions:

    "二叉树最长连续序列": https://leetcode.com/problems/binary-tree-longest-consecutive-sequence/

  ### Problem:

  给定一个未排序的整数数组 `nums` ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

  请你设计并实现时间复杂度为 `O(n)`的算法解决此问题。

  **示例 1：**

  ```
  输入：nums = [100,4,200,1,3,2]
  输出：4
  解释：最长数字连续序列是 [1, 2, 3, 4]。它的长度为 4。
  ```

  **示例 2：**

  ```
  输入：nums = [0,3,7,2,5,8,4,6,0,1]
  输出：9
  ```

  **提示：**

  - `0 <= nums.length <= 105`
  - `-109 <= nums[i] <= 109`

  ### Solution:

  

  *Template generated via [Leetmark](https://github.com/crimx/crx-leetmark).*

<!--more-->

### Solution:

https://leetcode-cn.com/problems/longest-consecutive-sequence/solution/zui-chang-lian-xu-xu-lie-by-leetcode-solution/

```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_set<int> num_set;
        for (const int& num : nums) {
            num_set.insert(num);
        }

        int longestStreak = 0;

        for (const int& num : num_set) {
            if (!num_set.count(num - 1)) {
                int currentNum = num;
                int currentStreak = 1;

                while (num_set.count(currentNum + 1)) {
                    currentNum += 1;
                    currentStreak += 1;
                }

                longestStreak = max(longestStreak, currentStreak);
            }
        }

        return longestStreak;           
    }
};
```