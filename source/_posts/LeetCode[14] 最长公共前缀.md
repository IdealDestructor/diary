---
title: LeetCode[14] 最长公共前缀
date: 2021-04-18 11:29:45
tags: [C++,LeetCode]
categories: 编程算法
---

### Related Topics:

"字符串": https://leetcode.com/tag/string/

### Problem:

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 `""`。

**示例 1：**

```
输入：strs = ["flower","flow","flight"]
输出："fl"
```

**示例 2：**

```
输入：strs = ["dog","racecar","car"]
输出：""
解释：输入不存在公共前缀。
```

**提示：**

- `0 <= strs.length <= 200`
- `0 <= strs[i].length <= 200`
- `strs[i]` 仅由小写英文字母组成

### Solution:

https://leetcode-cn.com/problems/longest-common-prefix/solution/zui-chang-gong-gong-qian-zhui-cshi-xian-shui-ping-/

```cpp
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        if(!strs.size()){
            return "";
        }
        int len = strs[0].size();
        int count = strs.size();
        for (int i = 0; i < len;i++){
            char c = strs[0][i];
            for (int j = 1; j < count; j++){
                if (i == strs[j].size() || strs[j][i] != c)
                    return strs[0].substr(0,i);
            }
        }
        return strs[0];
    }
};
```