---
title: LeetCode[42] 接雨水
date: 2021-10-05 21:03:35
tags: [C++,LeetCode]
categories: 编程算法
widgets: null
---

Related Topics:
  "栈": https://leetcode.com/tag/stack/
  "数组": https://leetcode.com/tag/array/
  "双指针": https://leetcode.com/tag/two-pointers/
  "动态规划": https://leetcode.com/tag/dynamic-programming/
  "单调栈": https://leetcode.com/tag/monotonic-stack/
Similar Questions:
  "盛最多水的容器": https://leetcode.com/problems/container-with-most-water/
  "除自身以外数组的乘积": https://leetcode.com/problems/product-of-array-except-self/
  "接雨水 II": https://leetcode.com/problems/trapping-rain-water-ii/
"倒水": https://leetcode.com/problems/pour-water/
---

### Problem:

给定 `n` 个非负整数表示每个宽度为 `1` 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

**示例 1：**

```
输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
输出：6
解释：上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。
```

**示例 2：**

```
输入：height = [4,2,0,3,2,5]
输出：9
```

**提示：**

- `n == height.length`
- `1 <= n <= 2 * 104`
- `0 <= height[i] <= 105`

<!--more-->

### Solution:

#### 方法一：动态规划

![截屏2021-10-05 下午8.55.59](https://i.loli.net/2021/10/05/CFpktJOfsmW7TRB.png)

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size();
        if (n == 0) {
            return 0;
        }
        vector<int> leftMax(n);
        leftMax[0] = height[0];
        for (int i = 1; i < n; ++i) {
            leftMax[i] = max(leftMax[i - 1], height[i]);
        }

        vector<int> rightMax(n);
        rightMax[n - 1] = height[n - 1];
        for (int i = n - 2; i >= 0; --i) {
            rightMax[i] = max(rightMax[i + 1], height[i]);
        }

        int ans = 0;
        for (int i = 0; i < n; ++i) {
            ans += min(leftMax[i], rightMax[i]) - height[i];
        }
        return ans;
    }
};
```

#### 方法二：单调栈

![截屏2021-10-05 下午8.00.31](https://i.loli.net/2021/10/05/QtSOkAU7YLM2xHE.png)

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        int ans = 0;
        stack<int> stk;
        int n = height.size();
        for (int i = 0; i < n; ++i) {
            while (!stk.empty() && height[i] > height[stk.top()]) {
                int top = stk.top();
                stk.pop();
                if (stk.empty()) {
                    break;
                }
                int left = stk.top();
                int currWidth = i - left - 1;
                int currHeight = min(height[left], height[i]) - height[top];
                ans += currWidth * currHeight;
            }
            stk.push(i);
        }
        return ans;
    }
};
```

#### 方法三：双指针

![截屏2021-10-05 下午8.02.39](https://i.loli.net/2021/10/05/BYhTrfxHctIbVOo.png)

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        int ans = 0;
        int left = 0, right = height.size() - 1;
        int leftMax = 0, rightMax = 0;
        while (left < right) {
            leftMax = max(leftMax, height[left]);
            rightMax = max(rightMax, height[right]);
            if (height[left] < height[right]) {
                ans += leftMax - height[left];
                ++left;
            } else {
                ans += rightMax - height[right];
                --right;
            }
        }
        return ans;
    }
};
```

