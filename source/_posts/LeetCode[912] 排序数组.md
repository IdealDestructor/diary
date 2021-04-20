---
title: LeetCode[912] 排序数组
date: 2021-03-27 14:22:35
tags: [C++,LeetCode]
categories: 编程算法
---

### Problem:

给你一个整数数组 `nums`，请你将该数组升序排列。

**示例 1：**

```
输入：nums = [5,2,3,1]
输出：[1,2,3,5]
```

**示例 2：**

```
输入：nums = [5,1,1,2,0,0]
输出：[0,0,1,1,2,5]
```

**提示：**

1. `1 <= nums.length <= 50000`
2. `50000 <= nums[i] <= 50000`

### Solution:

https://leetcode-cn.com/problems/sort-an-array/solution/pai-xu-shu-zu-by-leetcode-solution/

```cpp
/*
 * @lc app=leetcode.cn id=912 lang=cpp
 *
 * [912] 排序数组
 */

// @lc code=start
class Solution
{
    int partition(vector<int> &nums, int l, int r)
    {
        int pivot = nums[r];
        int i = l - 1;
        for (int j = l; j <= r - 1; ++j)
        {
            if (nums[j] <= pivot)
            {
                i = i + 1;
                swap(nums[i], nums[j]);
            }
        }
        swap(nums[i + 1], nums[r]);
        return i + 1;
    }
    int randomized_partition(vector<int> &nums, int l, int r)
    {
        int i = rand() % (r - l + 1) + l;
        swap(nums[r], nums[i]);
        return partition(nums, l, r);
    }
     void randomized_quicksort(vector<int>& nums, int l, int r) {
         if(l<r){
             int pos = randomized_partition(nums, l, r);
             randomized_quicksort(nums, l, pos-1);
             randomized_quicksort(nums, pos+1, r);
         }
     }
public:
    vector<int> sortArray(vector<int> &nums)
    {
        srand((unsigned)time(NULL));
        randomized_quicksort(nums, 0, (int)nums.size() - 1);
        return nums;
    }
}; 
// @lc code=end
```