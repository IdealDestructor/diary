---
title: LeetCode[56] 合并区间
date: 2021-03-27 14:22:35
tags: [C++,LeetCode,数组]
categories: 编程算法
widgets: null
---

### Related Topics:

  "排序": https://leetcode.com/tag/sort/
  "数组": https://leetcode.com/tag/array/
Similar Questions:
  "插入区间": https://leetcode.com/problems/insert-interval/
  "会议室": https://leetcode.com/problems/meeting-rooms/
  "会议室 II": https://leetcode.com/problems/meeting-rooms-ii/
  "提莫攻击": https://leetcode.com/problems/teemo-attacking/
  "给字符串添加加粗标签": https://leetcode.com/problems/add-bold-tag-in-string/
  "Range 模块": https://leetcode.com/problems/range-module/
  "员工空闲时间": https://leetcode.com/problems/employee-free-time/
  "划分字母区间": https://leetcode.com/problems/partition-labels/
  "区间列表的交集": https://leetcode.com/problems/interval-list-intersections/


### Problem:

以数组 `intervals` 表示若干个区间的集合，其中单个区间为 `intervals[i] = [starti, endi]` 。请你合并所有重叠的区间，并返回一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间。

**示例 1：**

```
输入：intervals = [[1,3],[2,6],[8,10],[15,18]]
输出：[[1,6],[8,10],[15,18]]
解释：区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
```

**示例 2：**

```
输入：intervals = [[1,4],[4,5]]
输出：[[1,5]]
解释：区间 [1,4] 和 [4,5] 可被视为重叠区间。
```

**提示：**

- `1 <= intervals.length <= 104`
- `intervals[i].length == 2`
- `0 <= starti <= endi <= 104`

<!--more-->

### Solution:

算法

我们用数组 merged 存储最终的答案。

首先，我们将列表中的区间按照左端点升序排序。然后我们将第一个区间加入 merged 数组中，并按顺序依次考虑之后的每个区间：

如果当前区间的左端点在数组 merged 中最后一个区间的右端点之后，那么它们不会重合，我们可以直接将这个区间加入数组 merged 的末尾；

否则，它们重合，我们需要用当前区间的右端点更新数组 merged 中最后一个区间的右端点，将其置为二者的较大值。

```c++
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
       sort(intervals.begin(), intervals.end());
        vector<vector<int>> merged;
        for(int i=0;i<intervals.size();++i){
            int L = intervals[i][0], R = intervals[i][1];
            if(!merged.size() || merged.back()[1] < L){
                merged.push_back({L,R});
            }else{
                merged.back()[1]=max(R,merged.back()[1]);
            }
        }
        return merged;
    }
};
```

