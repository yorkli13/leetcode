# 64. 最小路径和 (Minimum Path Sum)

## 题目描述

给定一个包含非负整数的 `m x n` 网格 `grid`，请找出一条从左上角到右下角的路径，使得路径上的数字总和最小。

说明：每次只能向下或者向右移动一步。

**示例 1：**

```text
输入：grid = [[1,3,1],[1,5,1],[4,2,1]]
输出：7
解释：因为路径 1 -> 3 -> 1 -> 1 -> 1 的总和最小
```

**示例 2：**

```text
输入：grid = [[1,2,3],[4,5,6]]
输出：12
```

**提示：**

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 200`
- `0 <= grid[i][j] <= 200`

## 算法知识

### 动态规划

这题和第 62、63 题属于同一类网格动态规划题。

区别在于：

- 第 62 题求的是“路径条数”
- 第 63 题求的是“有障碍时的路径条数”
- 这一题求的是“路径代价最小值”

所以网格结构没变，移动规则没变，变的只是状态含义和转移方式。

## 解法：二维动态规划求最小代价（推荐）

### 思路

定义：

```text
dp[i][j] = 从左上角走到位置 (i, j) 的最小路径和
```

因为每次只能向右或向下，所以到 `(i, j)` 的最后一步只可能来自：

- 上面 `(i - 1, j)`
- 左边 `(i, j - 1)`

既然要最小路径和，那我们就应该从这两个来源里选更小的那一个，再加上当前格子的值：

```text
dp[i][j] = min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j]
```

### 起点怎么处理

起点就是左上角 `(0, 0)`，从起点走到起点的最小路径和当然就是它本身：

```text
dp[0][0] = grid[0][0]
```

### 第一行和第一列怎么处理

因为机器人只能向右或向下走，所以：

- 第一行的每个位置只能从左边过来
- 第一列的每个位置只能从上边下来

所以：

```text
dp[0][j] = dp[0][j - 1] + grid[0][j]
dp[i][0] = dp[i - 1][0] + grid[i][0]
```

这和第 62 题“第一行第一列都是 1”不一样，因为这里累积的是路径和，不是路径条数。

### 普通位置怎么转移

当 `i > 0` 且 `j > 0` 时：

```text
dp[i][j] = min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j]
```

意思是：

1. 先看从上面走过来最便宜要多少
2. 再看从左边走过来最便宜要多少
3. 选更小的那个
4. 再加上当前格子的值

### 以示例 1 为例

```text
grid =
[
 [1,3,1],
 [1,5,1],
 [4,2,1]
]
```

先初始化起点：

```text
1 0 0
0 0 0
0 0 0
```

填完第一行和第一列：

```text
1 4 5
2 0 0
6 0 0
```

继续填中间位置：

- `dp[1][1] = min(4, 2) + 5 = 7`
- `dp[1][2] = min(5, 7) + 1 = 6`
- `dp[2][1] = min(7, 6) + 2 = 8`
- `dp[2][2] = min(6, 8) + 1 = 7`

最后得到：

```text
1 4 5
2 7 6
6 8 7
```

右下角就是答案：

```text
7
```

### 为什么这题适合用 DP

因为一个位置的最小路径和，完全依赖于更小子问题的答案：

- 到上面格子的最小路径和
- 到左边格子的最小路径和

而这些结果会被后面的格子反复使用，所以很适合用动态规划把它们存起来。

### 复杂度分析

- **时间复杂度：** `O(m * n)`，每个格子计算一次
- **空间复杂度：** `O(m * n)`，使用二维数组保存状态

## 代码

### C++

```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int m = grid.size();
        int n = grid[0].size();

        vector<vector<int>> dp(m, vector<int>(n, 0));
        dp[0][0] = grid[0][0];

        for (int j = 1; j < n; j++) {
            dp[0][j] = dp[0][j - 1] + grid[0][j];
        }

        for (int i = 1; i < m; i++) {
            dp[i][0] = dp[i - 1][0] + grid[i][0];
        }

        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j];
            }
        }

        return dp[m - 1][n - 1];
    }
};
```

### Python

```python
from typing import List


class Solution:
    def minPathSum(self, grid: List[List[int]]) -> int:
        m, n = len(grid), len(grid[0])
        dp = [[0] * n for _ in range(m)]
        dp[0][0] = grid[0][0]

        for j in range(1, n):
            dp[0][j] = dp[0][j - 1] + grid[0][j]

        for i in range(1, m):
            dp[i][0] = dp[i - 1][0] + grid[i][0]

        for i in range(1, m):
            for j in range(1, n):
                dp[i][j] = min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j]

        return dp[m - 1][n - 1]
```
