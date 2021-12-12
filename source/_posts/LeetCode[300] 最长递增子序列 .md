---
title: LeetCode[300] 最长递增子序列
date: 2021-12-12 22:29:45
tags: [C++,LeetCode,二分查找,贪心算法,动态规划]
categories: 编程算法
widgets: null
---

---
Related Topics:
  "数组": https://leetcode.com/tag/array/
  "二分查找": https://leetcode.com/tag/binary-search/
  "动态规划": https://leetcode.com/tag/dynamic-programming/
Similar Questions:
  "递增的三元子序列": https://leetcode.com/problems/increasing-triplet-subsequence/
  "俄罗斯套娃信封问题": https://leetcode.com/problems/russian-doll-envelopes/
  "最长数对链": https://leetcode.com/problems/maximum-length-of-pair-chain/
  "最长递增子序列的个数": https://leetcode.com/problems/number-of-longest-increasing-subsequence/
"两个字符串的最小ASCII删除和": https://leetcode.com/problems/minimum-ascii-delete-sum-for-two-strings/

### Problem:

给你一个整数数组 `nums` ，找到其中最长严格递增子序列的长度。

子序列是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，`[3,6,2,7]` 是数组 `[0,3,1,6,2,2,7]` 的子序列。

**示例 1：**

```
输入：nums = [10,9,2,5,3,7,101,18]
输出：4
解释：最长递增子序列是 [2,3,7,101]，因此长度为 4 。
```

**示例 2：**

```
输入：nums = [0,1,0,3,2,3]
输出：4
```

**示例 3：**

```
输入：nums = [7,7,7,7,7,7,7]
输出：1
```

**提示：**

- `1 <= nums.length <= 2500`
- `-104 <= nums[i] <= 104`

**进阶：**

- 你可以设计时间复杂度为 `O(n2)` 的解决方案吗？
- 你能将算法的时间复杂度降低到 `O(n log(n))` 吗?

<!--more-->

### Solution:

https://leetcode-cn.com/problems/longest-increasing-subsequence/solution/zui-chang-shang-sheng-zi-xu-lie-dong-tai-gui-hua-2/

```cpp
//动态规划
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return 0;
        vector<int> dp(n,0);
        for (int i = 0; i < n; ++i){
            dp[i] = 1;
            for (int j = 0; j < i; ++j){
                if(nums[j] < nums[i])
                dp[i] = max(dp[j]+1, dp[i]);
            }
        }
        return *max_element(dp.begin(), dp.end());
    }
//贪心+二分查找
int lengthOfLIS(vector<int>& nums) {
        int len = 1, n = nums.size();
        if (n == 0) return 0;
        vector<int> d(n+1,0);
        d[len] = nums[0];
        for (int i = 1; i < n; ++i) {
            if(nums[i] > d[len]) {
                d[++len] = nums[i];
            }else{
                int l = 1, r = len, pos = 0;
                while(l <= r) {
                    int mid = (l + r) >> 1;
                    if(d[mid] < nums[i]) {
                        pos = mid;
                        l = mid + 1;
                    }else{
                       r = mid - 1;
                    }
                }
                d[pos + 1] = nums[i];
            }
        }
        return len;
    }
};
```