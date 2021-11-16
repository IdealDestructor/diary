---
title: LeetCode[10] 正则表达式匹配
date: 2021-03-30 14:29:45
tags: [C++,LeetCode]
categories: 编程算法
widgets: null
---

### Related Topics:

"字符串": https://leetcode.com/tag/string/ "动态规划": https://leetcode.com/tag/dynamic-programming/ "回溯算法": https://leetcode.com/tag/backtracking/ Similar Questions: "通配符匹配": https://leetcode.com/problems/wildcard-matching/

### Problem:

给你一个字符串 `s` 和一个字符规律 `p`，请你来实现一个支持 `'.'` 和 `'*'` 的正则表达式匹配。

- `'.'` 匹配任意单个字符
- `'*'` 匹配零个或多个前面的那一个元素

所谓匹配，是要涵盖 **整个**字符串 `s`的，而不是部分字符串。

**示例 1：**

```
输入：s = "aa" p = "a"
输出：false
解释："a" 无法匹配 "aa" 整个字符串。
```

**示例 2:**

```
输入：s = "aa" p = "a*"
输出：true
解释：因为 '*' 代表可以匹配零个或多个前面的那一个元素, 在这里前面的元素就是 'a'。因此，字符串 "aa" 可被视为 'a' 重复了一次。
```

**示例 3：**

```
输入：s = "ab" p = ".*"
输出：true
解释：".*" 表示可匹配零个或多个（'*'）任意字符（'.'）。
```

**示例 4：**

```
输入：s = "aab" p = "c*a*b"
输出：true
解释：因为 '*' 表示零个或多个，这里 'c' 为 0 个, 'a' 被重复一次。因此可以匹配字符串 "aab"。
```

**示例 5：**

```
输入：s = "mississippi" p = "mis*is*p*."
输出：false
```

**提示：**

- `0 <= s.length <= 20`
- `0 <= p.length <= 30`
- `s` 可能为空，且只包含从 `a-z` 的小写字母。
- `p` 可能为空，且只包含从 `a-z` 的小写字母，以及字符 `.` 和 ``。
- 保证每次出现字符 `` 时，前面都匹配到有效的字符

<!--more-->

### Solution:

https://leetcode-cn.com/problems/regular-expression-matching/solution/zheng-ze-biao-da-shi-pi-pei-by-leetcode-solution/

```cpp
class Solution {
public:
    bool isMatch(string s, string p) {
        int sSize = int(s.size());
        int pSize = int(p.size());
        if (p.empty())
        {
            return s.empty();
        }
        vector<bool> tmpVec(pSize + 1, false);//dp大小应该比s，p的size大1
        vector<vector<bool>> dp(sSize + 1, tmpVec); //dp[i][j] 表示 s 的前 i 个是否能被 p 的前 j 个匹配
        dp[0][0] = true;
        if (sSize != 0 && (p[0] == s[0] || p[0] == '.'))
        {
            dp[1][1] = true;
        }
        if (p[0] == '*')
        {
            dp[0][1] = true;
        }
        //初始化情况：s为空，p为.*.*的情况
        for (int i = 1; i < pSize; i++)
        {
            if (p[i] == '*' && dp[0][i - 1] == true)
            {
                dp[0][i + 1] = true;
            }
        }

        for (int i = 0; i < sSize; i++)
        {
            for (int j = 1; j < pSize; j++)
            {
                if (p[j] == '.' || p[j] == s[i])
                { //如果是任意元素 或者是对于元素匹配
                    dp[i + 1][j + 1] = dp[i][j];
                }
                if (p[j] == '*')
                {
                    //caa cb* -> caa c
                    if (p[j - 1] != s[i] && p[j - 1] != '.')
                    { //如果前一个元素不匹配 且不为任意元素
                        dp[i + 1][j + 1] = dp[i + 1][j - 1];
                    }
                    else
                    {
                        //caa c.* -> ca c.*
                        //caa ca* -> ca ca*
                        //ca ca* -> ca ca / c ca*
                        //ca ca*a* -> ca ca*
                        dp[i + 1][j + 1] = (dp[i][j + 1] || dp[i + 1][j - 1]);// || dp[i + 1][j]不需要
                    }
                }
            }
        }
        //print(dp);
        return dp[sSize][pSize];
    }
};
```