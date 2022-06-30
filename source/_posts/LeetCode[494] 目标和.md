---
title: LeetCode[494] 目标和
date: 2022-06-16 14:22:35
tags: [JavaScript,LeetCode,动态规划]
categories: 编程算法
widgets: null
---

### Problem:

- 难度：中等

  给定一个非负整数数组，a1, a2, ..., an, 和一个目标数，S。现在你有两个符号 + 和 -。对于数组中的任意一个整数，你都可以从 + 或 -中选择一个符号添加在前面。

  返回可以使最终数组和为目标数 S 的所有添加符号的方法数。

  示例：

  输入：nums: [1, 1, 1, 1, 1], S: 3
  输出：5
  
  解释：
  -1+1+1+1+1 = 3
  +1-1+1+1+1 = 3
  +1+1-1+1+1 = 3
  +1+1+1-1+1 = 3
  +1+1+1+1-1 = 3
  
  一共有5种方法让最终目标和为3。
  
  提示：
  
  - 数组非空，且长度不会超过 20 。
  - 初始的数组的和不会超过 1000 。
  - 保证返回的最终结果能被 32 位整数存下。
  
  


<!--more-->

### Solution:

https://programmercarl.com/0494.目标和.html

```javascript
const findTargetSumWays = (nums, target) => {

    const sum = nums.reduce((a, b) => a+b);
    
    if(Math.abs(target) > sum) {
        return 0;
    }

    if((target + sum) % 2) {
        return 0;
    }

    const halfSum = (target + sum) / 2;

    let dp = new Array(halfSum+1).fill(0);
    dp[0] = 1;

    for(let i = 0; i < nums.length; i++) {
        for(let j = halfSum; j >= nums[i]; j--) {
            dp[j] += dp[j - nums[i]];
        }
    }

    return dp[halfSum];
};
```