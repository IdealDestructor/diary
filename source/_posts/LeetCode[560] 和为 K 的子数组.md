---
title: LeetCode[560] 和为 K 的子数组
date: 2022-06-01 14:22:35
tags: [JavaScript,LeetCode,数组,哈希表]
categories: 编程算法
widgets: null
---

### Problem:

给你一个整数数组 `nums` 和一个整数 `k` ，请你统计并返回 *该数组中和为 `k` 的子数组的个数* 。

 

**示例 1：**

```
输入：nums = [1,1,1], k = 2
输出：2
```

**示例 2：**

```
输入：nums = [1,2,3], k = 3
输出：2
```

 

**提示：**

- `1 <= nums.length <= 2 * 104`
- `-1000 <= nums[i] <= 1000`
- `-107 <= k <= 107`


<!--more-->

### Solution:

https://leetcode.cn/problems/subarray-sum-equals-k/solution/dai-ni-da-tong-qian-zhui-he-cong-zui-ben-fang-fa-y/

```cpp
const subarraySum = (nums, k) => {
    const map = { 0: 1 };
    let prefixSum = 0;
    let count = 0;
  
    for (let i = 0; i < nums.length; i++) {
      prefixSum += nums[i];
  
      if (map[prefixSum - k]) {
        count += map[prefixSum - k];
      }
  
      if (map[prefixSum]) {
        map[prefixSum]++;
      } else {
        map[prefixSum] = 1;
      }
    }
    return count;
  };
```