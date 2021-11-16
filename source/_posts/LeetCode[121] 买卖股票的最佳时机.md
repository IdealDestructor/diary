---
title: LeetCode[121] 买卖股票的最佳时机
date: 2021-04-23 21:03:35
tags: [C++,LeetCode]
categories: 编程算法
widgets: null
---

### Related Topics:

  "数组": https://leetcode.com/tag/array/
  "动态规划": https://leetcode.com/tag/dynamic-programming/
Similar Questions:
  "最大子序和": https://leetcode.com/problems/maximum-subarray/
  "买卖股票的最佳时机 II": https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/
  "买卖股票的最佳时机 III": https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/
  "买卖股票的最佳时机 IV": https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/
  "最佳买卖股票时机含冷冻期": https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/


### Problem:

给定一个数组 `prices` ，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 `i` 天的价格。

你只能选择 **某一天** 买入这只股票，并选择在 **未来的某一个不同的日子** 卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 `0` 。

**示例 1：**

```
输入：[7,1,5,3,6,4]
输出：5
解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
```

**示例 2：**

```
输入：prices = [7,6,4,3,1]
输出：0
解释：在这种情况下, 没有交易完成, 所以最大利润为 0。
```

**提示：**

- `1 <= prices.length <= 105`
- `0 <= prices[i] <= 104`

<!--more-->

### Solution:



*Template generated via [Leetmark](https://github.com/crimx/crx-leetmark).*

本题要求的其实是卖出时与买入时的最大差值。我们不妨假设f(i)表示以第i天为结尾时卖出股票获得的最大收入（差值），那么最后要求的是：

f(i) = max{f(i)| 1 <= i <= n>>};

而f(i)其实还与f(i-1)有关，f(i-1) = p[i-1] - minValue, 其中minValue表示以i-1结尾的前排数组的最小值，故 minValue = p[i-1] - f(i-1)。

那么我们可以得到如下关系： f(i) = max (p[i] - (p[i-1] - f(i-1), 0)) = max (f(i-1) + p[i] - p[i-1], 0); （显然，每次可以当天买进，当天卖出，所以最大收益不应该低于0。）

时间复杂度：O(N)； 空间复杂度：O(1)；

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int nowmaxincome=0,maxincome=0,diff = 0;
        for (int i=1;i<prices.size();i++){
            diff = prices[i]-prices[i-1];
            nowmaxincome = max(diff+nowmaxincome,0);//小于0意味着到现在已经跌底
            maxincome = max(maxincome,nowmaxincome);
        }
        return maxincome;
    }
};
```

