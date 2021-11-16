---
title: LeetCode[415]字符串相加
date: 2021-04-25 21:43:35
tags: [C++,LeetCode]
categories: 编程算法
widgets: null
---

### Related Topics:

  "字符串": https://leetcode.com/tag/string/
Similar Questions:
  "两数相加": https://leetcode.com/problems/add-two-numbers/
  "字符串相乘": https://leetcode.com/problems/multiply-strings/
  "数组形式的整数加法": https://leetcode.com/problems/add-to-array-form-of-integer/


### Problem:

给定两个字符串形式的非负整数 `num1` 和`num2` ，计算它们的和。

**提示：**

1. `num1` 和`num2` 的长度都小于 5100
2. `num1` 和`num2` 都只包含数字 `0-9`
3. `num1` 和`num2` 都不包含任何前导零
4. **你不能使用任何內建 BigInteger 库， 也不能直接将输入的字符串转换为整数形式**

<!--more-->

### Solution:

方法一：模拟
思路与算法
本题我们只需要对两个大整数模拟「坚式加法」的过程。坚式加法就是我们平常学习生活中常用的对两个整 数相加的方法，回想一下我们在纸上对两个整数相加的操作，是不是如下图将相同数位对齐，从低到高逐位 相加，如果当前位和超过 10, 则向高位进一位? 因此我们只要将这个过程用代码写出来即可。
具体实现也不复杂，我们定义两个指针 \(i\) 和 \(j\) 分别指向 \(n u m 1\) 和 \(n u m 2\) 的末尾，即最低位，同时定义一个变 量 \(a d d\) 维护当前是否有进位，然后从末尾到开头逐位相加即可。你可能会想两个数字位数不同怎么处理，这 里我们统一在指针当前下标处于负数的时候返回 0, 等价于对位数较短的数字进行了补零操作，这样就可以 除去两个数字位数不同情况的处理，具体可以看下面的代码。

```c++
class Solution
{
public:
    string addStrings(string num1, string num2)
    {
        int i = num1.length() - 1, j = num2.length() - 1, add = 0;
        string answer = "";
        while (i >= 0 || j >= 0 || add)
        {
            int x = i >= 0 ? num1[i] - '0' : 0;
            int y = j >= 0 ? num2[j] - '0' : 0;
            int result = x + y + add;
            answer += '0' + result % 10;
            add = result / 10;
            i-=1;
            j-=1;
        }
        //翻转
        reverse(answer.begin(), answer.end());
        return answer;
    }
};
```

复杂度分析
时间复杂度： $O(\max (l e n 1, l e n 2))$, 其中 $l e n 1=n u m 1 .$ length, $l e n 2=n u m_{2} .$ length $_{\circ}$ 坚式加法的次数取 决于较大数的位数。 空间复杂度： $O(1)$ 。除答案外我们只需要常数空间存放若干变量。在 Java 解法中使用到了 StringBuffer, 故 Java 解法的空间复杂度为 $O(n)$ 。