---
title: LeetCode[200] 岛屿数量
date: 2021-12-19 11:29:45
tags: [C++,LeetCode,并查集]
categories: 编程算法
widgets: null
---

Related Topics:
  "深度优先搜索": https://leetcode.com/tag/depth-first-search/
  "广度优先搜索": https://leetcode.com/tag/breadth-first-search/
  "并查集": https://leetcode.com/tag/union-find/
  "数组": https://leetcode.com/tag/array/
  "矩阵": https://leetcode.com/tag/matrix/
Similar Questions:
  "被围绕的区域": https://leetcode.com/problems/surrounded-regions/
  "墙与门": https://leetcode.com/problems/walls-and-gates/
  "岛屿数量 II": https://leetcode.com/problems/number-of-islands-ii/
  "无向图中连通分量的数目": https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/
  "不同岛屿的数量": https://leetcode.com/problems/number-of-distinct-islands/
 "岛屿的最大面积": https://leetcode.com/problems/max-area-of-island/

### Problem:

给你一个由 `'1'`（陆地）和 `'0'`（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。

**示例 1：**

```
输入：grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
输出：1
```

**示例 2：**

```
输入：grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
输出：3
```

**提示：**

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 300`
- `grid[i][j]` 的值为 `'0'` 或 `'1'

<!--more-->

### Solution:

https://leetcode-cn.com/problems/number-of-islands/solution/dao-yu-shu-liang-by-leetcode/

```c++
class Solution {
private:
    void dfs(vector<vector<char>>& grid, int r, int c) {
        int nr = grid.size();
        int nc = grid[0].size();

        grid[r][c] = '0';
        if (r - 1 >= 0 && grid[r-1][c] == '1') dfs(grid, r - 1, c);
        if (r + 1 < nr && grid[r+1][c] == '1') dfs(grid, r + 1, c);
        if (c - 1 >= 0 && grid[r][c-1] == '1') dfs(grid, r, c - 1);
        if (c + 1 < nc && grid[r][c+1] == '1') dfs(grid, r, c + 1);
    }

public:
    int numIslands(vector<vector<char>>& grid) {
        int nr = grid.size();
        if (!nr) return 0;
        int nc = grid[0].size();

        int num_islands = 0;
        for (int r = 0; r < nr; ++r) {
            for (int c = 0; c < nc; ++c) {
                if (grid[r][c] == '1') {
                    ++num_islands;
                    dfs(grid, r, c);
                }
            }
        }

        return num_islands;
    }
};
```

