---
title: LeetCode[5] 最长回文子串
date: 2021-02-09 22:29:45
tags: [C++,LeetCode]
categories: 编程算法
---

### Related Topics:

"字符串": https://leetcode.com/tag/string/ "动态规划": https://leetcode.com/tag/dynamic-programming/ Similar Questions: "最短回文串": https://leetcode.com/problems/shortest-palindrome/ "回文排列": https://leetcode.com/problems/palindrome-permutation/ "回文对": https://leetcode.com/problems/palindrome-pairs/ "最长回文子序列": https://leetcode.com/problems/longest-palindromic-subsequence/ "回文子串": https://leetcode.com/problems/palindromic-substrings/

### Problem:

给你一个字符串 `s`，找到 `s` 中最长的回文子串。

**示例 1：**

```
输入：s = "babad"
输出："bab"
解释："aba" 同样是符合题意的答案。
```

**示例 2：**

```
输入：s = "cbbd"
输出："bb"
```

**示例 3：**

```
输入：s = "a"
输出："a"
```

**示例 4：**

```
输入：s = "ac"
输出："a"
```

**提示：**

- `1 <= s.length <= 1000`
- `s` 仅由数字和英文字母（大写和/或小写）组成

<!--more-->

### Solution:

https://leetcode-cn.com/problems/longest-palindromic-substring/solution/zui-chang-hui-wen-zi-chuan-by-leetcode-solution/

```cpp
class Solution {
public:
    string longestPalindrome(string s) {
        int n = s.size();
        vector<vector<int>> dp(n, vector<int>(n));
        string ans;
        for (int l = 0; l < n; ++l) {
            for (int i = 0; i + l < n; ++i) {
                int j = i + l;
                if (l == 0) {
                    dp[i][j] = 1;
                }
                else if (l == 1) {
                    dp[i][j] = (s[i] == s[j]);
                }
                else {
                    dp[i][j] = (s[i] == s[j] && dp[i + 1][j - 1]);
                }
                if (dp[i][j] && l + 1 > ans.size()) {
                    ans = s.substr(i, l + 1);
                }
            }
        }
        return ans;
    }
};
```