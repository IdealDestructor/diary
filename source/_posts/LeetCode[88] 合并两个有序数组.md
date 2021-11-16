---
title: LeetCode[88] 合并两个有序数组
date: 2021-04-28 18:43:35
tags: [C++,LeetCode]
categories: 编程算法
widgets: null
---

### Related Topics:

  "数组": https://leetcode.com/tag/array/
  "双指针": https://leetcode.com/tag/two-pointers/
Similar Questions:
  "合并两个有序链表": https://leetcode.com/problems/merge-two-sorted-lists/
  "有序数组的平方": https://leetcode.com/problems/squares-of-a-sorted-array/
  "区间列表的交集": https://leetcode.com/problems/interval-

### Problem:

给你两个有序整数数组 `nums1`和 `nums2`，请你将 `nums2`合并到 `nums1`中*，*使 `nums1`成为一个有序数组。

初始化 `nums1` 和 `nums2` 的元素数量分别为 `m` 和 `n`。你可以假设 `nums1`的空间大小等于 `m + n`，这样它就有足够的空间保存来自 `nums2` 的元素。

**示例 1：**

```
输入：nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
输出：[1,2,2,3,5,6]
```

**示例 2：**

```
输入：nums1 = [1], m = 1, nums2 = [], n = 0
输出：[1]
```

**提示：**

- `nums1.length == m + n`

- `nums2.length == n`

- `0 <= m, n <= 200`

- `1 <= m + n <= 200`

- `-109 <= nums1[i], nums2[i] <= 109`

  <!--more-->

### Solution:

https://leetcode-cn.com/problems/merge-sorted-array/solution/he-bing-liang-ge-you-xu-shu-zu-by-leetco-rrb0/

```c++
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int p1 = m-1, p2 = n-1;
        int tail = m+n-1;
        int temp;
        while(p1>=0||p2>=0){
            temp = p1==-1?nums2[p2--]:(p2==-1?nums1[p1--]:(nums1[p1]>nums2[p2]?nums1[p1--]:nums2[p2--]));
            nums1[tail--]=temp;
        }
    }
};
```

