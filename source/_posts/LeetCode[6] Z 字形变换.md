---
title: LeetCode[6] Z 字形变换
date: 2021-02-11 17:29:45
tags: [C++,LeetCode]
categories: 编程算法
---

### Related Topics:

"字符串": https://leetcode.com/tag/string/

### Problem:

将一个给定字符串 `s` 根据给定的行数 `numRows` ，以从上往下、从左到右进行 Z 字形排列。

比如输入字符串为 `"PAYPALISHIRING"` 行数为 `3` 时，排列如下：

```
P   A   H   N
A P L S I I G
Y   I   R
```

之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如：`"PAHNAPLSIIGYIR"`。

请你实现这个将字符串进行指定行数变换的函数：

```
string convert(string s, int numRows);
```

**示例 1：**

```
输入：s = "PAYPALISHIRING", numRows = 3
输出："PAHNAPLSIIGYIR"
```

**示例 2：**

```
输入：s = "PAYPALISHIRING", numRows = 4
输出："PINALSIGYAHRPI"
解释：
P     I    N
A   L S  I G
Y A   H R
P     I
```

**示例 3：**

```
输入：s = "A", numRows = 1
输出："A"
```

**提示：**

- `1 <= s.length <= 1000`
- `s` 由英文字母（小写和大写）、`','` 和 `'.'` 组成
- `1 <= numRows <= 1000`

### Solution:

https://leetcode-cn.com/problems/zigzag-conversion/solution/z-zi-xing-bian-huan-by-leetcode/

```cpp
class Solution(object):
    def convert(self, s, numRows):
        if numRows==1 or numRows >= len(s):
            return s
        rows = [''] * numRows
        cur_row = 0
        going_down = False
        for c in s:
            rows[cur_row] +=c
            if cur_row ==0 or cur_row == numRows -1:
                going_down = not going_down
            cur_row += 1 if going_down else -1
        res = ''.join(rows)
        return res
```