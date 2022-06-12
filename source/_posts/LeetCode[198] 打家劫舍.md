---
title: LeetCode[198] 打家劫舍
date: 2021-12-04 17:05:45
tags: [C++,LeetCode,动态规划]
categories: 编程算法
widgets: null
---

Related Topics:
  "数组": https://leetcode.com/tag/array/
  "动态规划": https://leetcode.com/tag/dynamic-programming/
Similar Questions:
  "乘积最大子数组": https://leetcode.com/problems/maximum-product-subarray/
  "打家劫舍 II": https://leetcode.com/problems/house-robber-ii/
  "粉刷房子": https://leetcode.com/problems/paint-house/
  "栅栏涂色": https://leetcode.com/problems/paint-fence/
  "打家劫舍 III": https://leetcode.com/problems/house-robber-iii/
  "不含连续1的非负整数": https://leetcode.com/problems/non-negative-integers-without-consecutive-ones/
  "金币路径": https://leetcode.com/problems/coin-path/
  "删除并获得点数": https://leetcode.com/problems/delete-and-earn/

### Problem:

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。

给定一个代表每个房屋存放金额的非负整数数组，计算你**不触动警报装置的情况下**，一夜之内能够偷窃到的最高金额。

**示例 1：**

```
输入：[1,2,3,1]
输出：4
解释：偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
     偷窃到的最高金额 = 1 + 3 = 4 。
```

**示例 2：**

```
输入：[2,7,9,3,1]
输出：12
解释：偷窃 1 号房屋 (金额 = 2), 偷窃 3 号房屋 (金额 = 9)，接着偷窃 5 号房屋 (金额 = 1)。
     偷窃到的最高金额 = 2 + 9 + 1 = 12 。
```

**提示：**

- `1 <= nums.length <= 100`
- `0 <= nums[i] <= 400`

<!--more-->

### Solution:

https://leetcode-cn.com/problems/house-robber/solution/da-jia-jie-she-by-leetcode-solution/

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return 0;
        if (n == 1) return nums[0];
        
        // vector<int> dp(n, 0);
        // dp[0] = nums[0];
        // dp[1] = max(nums[0], nums[1]);
        // for (int i = 2; i < n; i++) {
        //     dp[i] = max(nums[i] + dp[i - 2], dp[i - 1]);
        // }
        // return dp[n - 1];
        //不使用数组
        int firstSum = nums[0],secondSum = max(nums[1], nums[0]);
        for (int i = 2; i < n; i++) {
            int temp = max(firstSum + nums[i], secondSum);
            firstSum = secondSum;
            secondSum = temp;
        }
        return secondSum;
    }
};
```
https://programmercarl.com/0198.打家劫舍.html

```javascript
//js
const rob = nums => {
    // 数组长度
    const len = nums.length;
    // dp数组初始化
    const dp = [nums[0], Math.max(nums[0], nums[1])];
    // 从下标2开始遍历
    for (let i = 2; i < len; i++) {
        dp[i] = Math.max(dp[i - 2] + nums[i], dp[i - 1]);
    }
    return dp[len - 1];
};
```

