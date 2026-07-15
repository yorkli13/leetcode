# 63. 不同路径 II (Unique Paths II)

## 题目描述

一个机器人位于一个 `m x n` 网格的左上角，起始位置在 `(0, 0)`。

机器人每次只能向下或者向右移动一步。机器人试图到达网格的右下角，也就是 `(m - 1, n - 1)`。

现在网格中有障碍物。`obstacleGrid[i][j] = 1` 表示该位置有障碍，`obstacleGrid[i][j] = 0` 表示该位置可以通行。

请问总共有多少条不同的路径？

**示例 1：**

```text
输入：obstacleGrid = [[0,0,0],[0,1,0],[0,0,0]]
输出：2
解释：
3x3 网格中间有一个障碍物
从左上角到右下角一共有 2 条不同的路径：
1. 向右 -> 向右 -> 向下 -> 向下
2. 向下 -> 向下 -> 向右 -> 向右
```

**示例 2：**

```text
输入：obstacleGrid = [[0,1],[0,0]]
输出：1
```

**提示：**

- `m == obstacleGrid.length`
- `n == obstacleGrid[i].length`
- `1 <= m, n <= 100`
- `obstacleGrid[i][j]` 为 `0` 或 `1`

## 算法知识

### 动态规划

这题其实就是第 62 题“不同路径”的升级版。

第 62 题没有障碍物，所以每个位置都可以走到。
而这题多了障碍格子，因此要额外考虑：

- 如果当前格子是障碍物，那么它的路径数一定是 0
- 否则它的路径数仍然来自“上面 + 左边”

所以主框架还是动态规划，只是多了一层障碍判断。

## 解法：带障碍判断的二维动态规划（推荐）

### 思路

定义：

```text
dp[i][j] = 到达位置 (i, j) 的不同路径数
```

对于每个格子，分两种情况：

1. 如果这个格子是障碍物
   - 那么根本不能走到这里
   - 所以 `dp[i][j] = 0`
2. 如果不是障碍物
   - 那它的路径数依然来自上边和左边

### 起点怎么处理

如果起点本身就是障碍物：

```text
obstacleGrid[0][0] == 1
```

那么机器人连出发都做不到，答案直接是 `0`。

否则起点的路径数应该是：

```text
dp[0][0] = 1
```

表示“站在起点”本身算一种开始状态。

### 状态转移

当某个格子不是障碍物时：

```text
dp[i][j] = 上面的路径数 + 左边的路径数
```

也就是：

```text
dp[i][j] = dp[i - 1][j] + dp[i][j - 1]
```

当然，前提是这些位置存在。

为了写代码方便，我们可以在遍历每个位置时：

- 如果有上边，就加 `dp[i - 1][j]`
- 如果有左边，就加 `dp[i][j - 1]`

### 为什么第一行和第一列不能直接全设成 1

这点和第 62 题不一样。

在没有障碍物时：

- 第一行只能一直往右走
- 第一列只能一直往下走

所以它们都可以初始化成 1。

但这题如果某个位置出现障碍物，比如第一行中间有障碍，那么障碍物右边的位置也都到不了了。

例如：

```text
[0, 1, 0, 0]
```

一旦走到障碍物 `1`，后面的格子虽然不是障碍，但也没法从左边穿过去。

所以这题更稳妥的写法是统一用通用转移来处理，而不是直接整行整列赋成 1。

### 以示例 1 为例

```text
obstacleGrid =
[
 [0,0,0],
 [0,1,0],
 [0,0,0]
]
```

起点 `(0,0)` 不是障碍，所以：

```text
dp[0][0] = 1
```

继续填表：

```text
1 1 1
1 0 1
1 1 2
```

中间的障碍物位置路径数是 `0`。

最后右下角的值是：

```text
2
```

所以答案就是 2。

### 复杂度分析

- **时间复杂度：** `O(m * n)`，每个格子只计算一次
- **空间复杂度：** `O(m * n)`，使用二维数组保存状态

## 代码

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        int m = obstacleGrid.size();
        int n = obstacleGrid[0].size();

        vector<vector<int>> dp(m, vector<int>(n, 0));

        if (obstacleGrid[0][0] == 1) {
            return 0;
        }
        dp[0][0] = 1;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (obstacleGrid[i][j] == 1) {
                    dp[i][j] = 0;
                    continue;
                }

                if (i == 0 && j == 0) {
                    continue;
                }

                if (i > 0) {
                    dp[i][j] += dp[i - 1][j];
                }
                if (j > 0) {
                    dp[i][j] += dp[i][j - 1];
                }
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
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        m, n = len(obstacleGrid), len(obstacleGrid[0])
        dp = [[0] * n for _ in range(m)]

        if obstacleGrid[0][0] == 1:
            return 0
        dp[0][0] = 1

        for i in range(m):
            for j in range(n):
                if obstacleGrid[i][j] == 1:
                    dp[i][j] = 0
                    continue

                if i == 0 and j == 0:
                    continue

                if i > 0:
                    dp[i][j] += dp[i - 1][j]
                if j > 0:
                    dp[i][j] += dp[i][j - 1]

        return dp[m - 1][n - 1]
```
