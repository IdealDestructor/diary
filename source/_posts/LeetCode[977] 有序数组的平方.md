---
title: LeetCode[977] 有序数组的平方
date: 2022-06-02 14:22:35
tags: [C++,LeetCode,数组,双指针]
categories: 编程算法
widgets: null
---

### Problem:

给你一个按 非递减顺序 排序的整数数组 nums，返回 每个数字的平方 组成的新数组，要求也按 非递减顺序 排序。

 

示例 1：

输入：nums = [-4,-1,0,3,10]
输出：[0,1,9,16,100]
解释：平方后，数组变为 [16,1,0,9,100]
排序后，数组变为 [0,1,9,16,100]
示例 2：

输入：nums = [-7,-3,2,3,11]
输出：[4,9,9,49,121]


提示：

1 <= nums.length <= 104
-104 <= nums[i] <= 104
nums 已按 非递减顺序 排序




<!--more-->

### Solution:

https://programmercarl.com/0977.有序数组的平方.html

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