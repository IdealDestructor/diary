---
title: LeetCode[283] 移动零
date: 2021-12-03 17:05:45
tags: [C++,LeetCode,双指针]
categories: 编程算法
widgets: null
---

Related Topics:
  "数组": https://leetcode.com/tag/array/
  "双指针": https://leetcode.com/tag/two-pointers/
Similar Questions:
  "移除元素": https://leetcode.com/problems/remove-element/

### Problem:

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。

**示例:**

```
输入: [0,1,0,3,12]
输出: [1,3,12,0,0]
```

**说明**:

1. 必须在原数组上操作，不能拷贝额外的数组。
2. 尽量减少操作次数。

<!--more-->

### Solution:

https://leetcode-cn.com/problems/move-zeroes/solution/yi-dong-ling-by-leetcode-solution/

```c++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int left = 0, right = 0, n = nums.size();
        while(right < n){
            if(nums[right]){
                swap(nums[left],nums[right]);
                left++;
            }
            right++;
        }
    }
};
```

