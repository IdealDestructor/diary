---
title: LeetCode[406] 根据身高重建队列
date: 2021-12-06 17:05:45
tags: [C++,LeetCode,数组]
categories: 编程算法
widgets: null
---

Related Topics:
  "贪心": https://leetcode.com/tag/greedy/
  "数组": https://leetcode.com/tag/array/
  "排序": https://leetcode.com/tag/sorting/
Similar Questions:
 "计算右侧小于当前元素的个数": https://leetcode.com/problems/count-of-smaller-numbers-after-self/

### Problem:

假设有打乱顺序的一群人站成一个队列，数组 `people` 表示队列中一些人的属性（不一定按顺序）。每个 `people[i] = [hi, ki]` 表示第 `i` 个人的身高为 `hi` ，前面 **正好** 有 `ki` 个身高大于或等于 `hi` 的人。

请你重新构造并返回输入数组 `people` 所表示的队列。返回的队列应该格式化为数组 `queue` ，其中 `queue[j] = [hj, kj]` 是队列中第 `j` 个人的属性（`queue[0]` 是排在队列前面的人）。

**示例 1：**

```
输入：people = [[7,0],[4,4],[7,1],[5,0],[6,1],[5,2]]
输出：[[5,0],[7,0],[5,2],[6,1],[4,4],[7,1]]
解释：
编号为 0 的人身高为 5 ，没有身高更高或者相同的人排在他前面。
编号为 1 的人身高为 7 ，没有身高更高或者相同的人排在他前面。
编号为 2 的人身高为 5 ，有 2 个身高更高或者相同的人排在他前面，即编号为 0 和 1 的人。
编号为 3 的人身高为 6 ，有 1 个身高更高或者相同的人排在他前面，即编号为 1 的人。
编号为 4 的人身高为 4 ，有 4 个身高更高或者相同的人排在他前面，即编号为 0、1、2、3 的人。
编号为 5 的人身高为 7 ，有 1 个身高更高或者相同的人排在他前面，即编号为 1 的人。
因此 [[5,0],[7,0],[5,2],[6,1],[4,4],[7,1]] 是重新构造后的队列。
```

**示例 2：**

```
输入：people = [[6,0],[5,0],[4,0],[3,2],[2,2],[1,4]]
输出：[[4,0],[5,0],[2,2],[3,2],[1,4],[6,0]]
```

**提示：**

- `1 <= people.length <= 2000`
- `0 <= hi <= 106`
- `0 <= ki < people.length`
- 题目数据确保队列可以被重建

<!--more-->

### Solution:

https://leetcode-cn.com/problems/queue-reconstruction-by-height/solution/xian-pai-xu-zai-cha-dui-dong-hua-yan-shi-suan-fa-g/

```c++
class mycompare
{
public:
    bool operator()(vector<int> a, vector<int> b)
    {
        if (a[0] != b[0])
        {
            return a[0] > b[0];
        }
        return a[1] < b[1];
    }
};
class Solution
{
public:
    vector<vector<int> > reconstructQueue(vector<vector<int> > &people)
    {
        sort(people.begin(), people.end(), mycompare());
        vector<vector<int> > ans;
        for (int i = 0; i < people.size(); i++)
        {
            if (people[i][1] >= ans.size())
            {
                ans.push_back(people[i]);
            }
            else
            {
                ans.insert(ans.begin() + people[i][1], people[i]);
            }
        }
        return ans;
    }
};
// class Solution {
// public:
    
//     vector<vector<int>> reconstructQueue(vector<vector<int>>& nums) {
//         int n = nums.size();
//         vector<vector<int>> ans;
//         sort(nums.begin(), nums.end(), [](const vector<int>& a, const vector<int>& b) {
//             if (a[0] != b[0]) {
//                 return a[0] > b[0];
//             }
//             return a[1] < b[1];
//         });
//         for(auto& i : nums){
//             if(ans.size() <= i[1])
//             ans.push_back(i);
//             else{
//                ans.insert(ans.begin()+i[1],i); 
//             }
//         }
//         return ans;

//     }
// };
```

