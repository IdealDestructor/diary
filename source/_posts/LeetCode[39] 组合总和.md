---
title: LeetCode[39] 组合总和
date: 2022-06-20 14:22:35
tags: [JavaScript,LeetCode,回溯]
categories: 编程算法
widgets: null
---

### Problem:

给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

candidates 中的数字可以无限制重复被选取。

说明：

- 所有数字（包括 target）都是正整数。
- 解集不能包含重复的组合。

示例 1： 输入：candidates = [2,3,6,7], target = 7, 所求解集为： [ [7], [2,2,3] ]

示例 2： 输入：candidates = [2,3,5], target = 8, 所求解集为： [  [2,2,2,2],  [2,3,3],  [3,5] ]

<!--more-->

### Solution:

https://programmercarl.com/0039.组合总和.html

```cpp
var combinationSum = function(candidates, target) {
    const res = [], path = [];
    candidates.sort((a,b)=>a-b); // 排序
    backtracking(0, 0);
    return res;
    function backtracking(j, sum) {
        if (sum === target) {
            res.push(Array.from(path));
            return;
        }
        for(let i = j; i < candidates.length; i++ ) {
            const n = candidates[i];
            if(n > target - sum) break;
            path.push(n);
            sum += n;
            backtracking(i, sum);
            path.pop();
            sum -= n;
        }
    }
};
```