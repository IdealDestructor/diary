---
title: LeetCode[704] 二分查找
date: 2021-11-29 11:29:45
tags: [C++,LeetCode]
categories: 编程算法
widgets: null
---

---
Related Topics:
  "数组": https://leetcode.com/tag/array/
  "二分查找": https://leetcode.com/tag/binary-search/
Similar Questions:
  "搜索长度未知的有序数组": https://leetcode.com/problems/search-in-a-sorted-array-of-unknown-size/

### Problem:

给定一个 `n` 个元素有序的（升序）整型数组 `nums` 和一个目标值 `target`  ，写一个函数搜索 `nums` 中的 `target`，如果目标值存在返回下标，否则返回 `-1`。

**示例 1:**

```
输入: nums = [-1,0,3,5,9,12], target = 9
输出: 4
解释: 9 出现在 nums 中并且下标为 4
```

**示例 2:**

```
输入: nums = [-1,0,3,5,9,12], target = 2
输出: -1
解释: 2 不存在 nums 中因此返回 -1
```

**提示：**

1. 你可以假设 `nums` 中的所有元素是不重复的。
2. `n` 将在 `[1, 10000]`之间。
3. `nums` 的每个元素都将在 `[-9999, 9999]`之间。<!--more-->

### Solution:

https://leetcode-cn.com/problems/binary-search/solution/er-fen-cha-zhao-by-leetcode-solution-f0xw/

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        // 左闭右闭
    //    int left = 0;
    //     int right = nums.size() - 1; // 定义target在左闭右闭的区间里，[left, right]
    //     while (left <= right) { // 当left==right，区间[left, right]依然有效，所以用 <=
    //         int middle = left + ((right - left) / 2);// 防止溢出 等同于(left + right)/2
    //         if (nums[middle] > target) {
    //             right = middle - 1; // target 在左区间，所以[left, middle - 1]
    //         } else if (nums[middle] < target) {
    //             left = middle + 1; // target 在右区间，所以[middle + 1, right]
    //         } else { // nums[middle] == target
    //             return middle; // 数组中找到目标值，直接返回下标
    //         }
    //     }
    //     // 未找到目标值
    //     return -1;
        // 左闭右开
        int left = 0;
        int right = nums.size(); // 定义target在左闭右闭的区间里，[left, right]
        while (left < right) { // 当left==right，区间[left, right]依然有效，所以用 <=
            int middle = left + ((right - left) / 2);// 防止溢出 等同于(left + right)/2
            if (nums[middle] > target) {
                right = middle; // target 在左区间，所以[left, middle - 1]
            } else if (nums[middle] < target) {
                left = middle + 1; // target 在右区间，所以[middle + 1, right]
            } else { // nums[middle] == target
                return middle; // 数组中找到目标值，直接返回下标
            }
        }
        // 未找到目标值
        return -1;
    }
};
```

