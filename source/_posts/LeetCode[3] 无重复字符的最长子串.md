---
title: LeetCode[3] 无重复字符的最长子串
date: 2021-03-29 12:22:35
tags: [C++,LeetCode]
categories: 编程算法

---

### Related Topics:

  "哈希表": https://leetcode.com/tag/hash-table/
  "双指针": https://leetcode.com/tag/two-pointers/
  "字符串": https://leetcode.com/tag/string/
  "Sliding Window": https://leetcode.com/tag/sliding-window/
Similar Questions:
  "至多包含两个不同字符的最长子串": https://leetcode.com/problems/longest-substring-with-at-most-two-distinct-characters/
  "至多包含 K 个不同字符的最长子串": https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/
  "K 个不同整数的子数组": https://leetcode.com/problems/subarrays-with-k-different-integers/

### Problem:

给定一个字符串，请你找出其中不含有重复字符的 **最长子串**的长度。

**示例 1:**

```
输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

**示例 2:**

```
输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```

**示例 3:**

```
输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

**示例 4:**

```
输入: s = ""
输出: 0
```

**提示：**

- `0 <= s.length <= 5 * 104`
- `s` 由英文字母、数字、符号和空格组成



<!--more-->

### Solution:

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        if(s.size() == 0) return 0;
        unordered_set<char> lookup;
        int maxStr = 0;
        int left = 0;
        for(int i = 0; i < s.size(); i++){
            while (lookup.find(s[i]) != lookup.end()){
                lookup.erase(s[left]);
                left ++;
            }
            maxStr = max(maxStr,i-left+1);
            lookup.insert(s[i]);
    }
        return maxStr;
        
    }
};
```



```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        // 哈希集合，记录每个字符是否出现过
        unordered_set<char> occ;
        int n = s.size();
        // 右指针，初始值为 -1，相当于我们在字符串的左边界的左侧，还没有开始移动
        int rk = -1, ans = 0;
        // 枚举左指针的位置，初始值隐性地表示为 -1
        for (int i = 0; i < n; ++i) {
            if (i != 0) {
                // 左指针向右移动一格，移除一个字符
                occ.erase(s[i - 1]);
            }
            while (rk + 1 < n && !occ.count(s[rk + 1])) {
                // 不断地移动右指针
                occ.insert(s[rk + 1]);
                ++rk;
            }
            // 第 i 到 rk 个字符是一个极长的无重复字符子串
            ans = max(ans, rk - i + 1);
        }
        return ans;
    }
};

```



```c++
// 解法2：动态规划 O(N)
func lengthOfLongestSubstring(s string) int {
	n := len(s)
	if n <= 1 {
		return n
	}
	ans := 1
	lastIndexMap := make([]int, 256) // 字符上一次出现的位置表
	for i := 0; i < 256; i++ {
		lastIndexMap[i] = -1 // 初始：-1，标记都没出现过
	}
        // dp含义：dp[i]表示 以i位置做结尾的不重复子串的开始位置，即：[dp[i] ... i]是以i结尾时的最长不重复子串
	dp := make([]int, n) 
	lastIndexMap[s[0]] = 0
	dp[0] = 0 // [0...0]不重复
	for i := 1; i < n; i++ {
                // i向前最远能推到哪里？1）i-1向前推到的位置；2）[i]上一次出现的位置的下一个位置；二者取决于瓶颈：较大者（最靠近i的）
		dp[i] = max(dp[i-1], lastIndexMap[s[i]]+1)
		lastIndexMap[s[i]] = i
		ans = max(ans, i - dp[i] + 1)
	}
	return ans
}
```


