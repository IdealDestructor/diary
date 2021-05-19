---
title: LeetCode[718] 最长重复子数组
date: 2021-05-19 21:36:35
tags: [C++,LeetCode]
categories: 编程算法
---

### Related Topics:
  "数组": https://leetcode.com/tag/array/
  "哈希表": https://leetcode.com/tag/hash-table/
  "二分查找": https://leetcode.com/tag/binary-search/
  "动态规划": https://leetcode.com/tag/dynamic-programming/
Similar Questions:
  "长度最小的子数组": https://leetcode.com/problems/minimum-size-subarray-sum/


### Problem:

给两个整数数组 `A` 和 `B` ，返回两个数组中公共的、长度最长的子数组的长度。

**示例：**

```
输入：
A: [1,2,3,2,1]
B: [3,2,1,4,7]
输出：3
解释：
长度最长的公共子数组是 [3, 2, 1] 。
```

**提示：**

- `1 <= len(A), len(B) <= 1000`
- `0 <= A[i], B[i] < 100`

### Solution:

```c++
class Solution {
public:
    int findLength(vector<int>& nums1, vector<int>& nums2) {
        int n = nums1.size(), m = nums2.size();
        int ans = 0;
        vector<vector<int>> dp(n+1, vector<int>(m+1, 0));
        for (int i = n-1; i >=0; i--){
            for (int j = m-1; j >=0; j--){
                dp[i][j] = nums1[i]==nums2[j] ? dp[i + 1][j + 1] + 1 : 0;
                ans = max(ans, dp[i][j]);
            }
        }
        return ans;
    }
};
```


