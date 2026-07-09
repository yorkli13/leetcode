# 51. N 皇后 (N-Queens)

## 题目描述

按照国际象棋的规则，皇后可以攻击与之处在同一行、同一列、同一斜线上的棋子。

给你一个整数 `n`，返回所有不同的 `n` 皇后问题的解。

每一种解法包含一个不同的 `n x n` 棋盘布局，其中：

- `'Q'` 表示皇后
- `'.'` 表示空位

**示例 1：**

```text
输入：n = 4
输出：
[
 [".Q..","...Q","Q...","..Q."],
 ["..Q.","Q...","...Q",".Q.."]
]
```

**示例 2：**

```text
输入：n = 1
输出：[["Q"]]
```

**提示：**

- `1 <= n <= 9`

## 算法知识

### 回溯

这题需要列出所有合法棋盘，本质上是一个“枚举所有方案”的问题。

而且每放一个皇后，后面的位置可选范围都会变小。

这种：

1. 尝试放置
2. 继续递归
3. 如果后面不合法就撤销

的过程，就是典型的回溯。

### 状态记录

如果每次放一个皇后时，都去扫描整张棋盘判断是否合法，会比较慢，也不利于理解。

更好的办法是直接记录：

1. 哪些列已经有皇后
2. 哪些主对角线已经有皇后
3. 哪些副对角线已经有皇后

这样判断某个位置 `(row, col)` 能不能放皇后，就能在 `O(1)` 时间完成。

## 解法：按行回溯 + 列和对角线标记（推荐）

### 思路

我们按行来放皇后。

因为每一行最终都必须放且只放一个皇后，所以可以递归处理：

- 第 0 行放哪里
- 第 1 行放哪里
- 第 2 行放哪里
- ...

对于当前行 `row`，尝试每一列 `col`：

1. 如果这一列已经有皇后，不能放
2. 如果对应主对角线已经有皇后，不能放
3. 如果对应副对角线已经有皇后，不能放
4. 否则就可以放皇后，然后递归处理下一行

当 `row == n` 时，说明前面每一行都已经成功放了一个皇后，此时得到一个完整解。

### 对角线编号怎么表示

这是这题最关键的地方之一。

对于位置 `(row, col)`：

#### 主对角线

同一条主对角线满足：

```text
row - col 相同
```

但 `row - col` 可能是负数，所以通常加上一个偏移量：

```text
diag1 = row - col + n - 1
```

#### 副对角线

同一条副对角线满足：

```text
row + col 相同
```

这个值天然不会是负数，所以可以直接用：

```text
diag2 = row + col
```

### 为什么按行放就够了

因为每一层递归只处理一行，所以天然保证了：

- 每一行只会放一个皇后

再结合列和对角线的限制，就能保证整个棋盘合法。

所以不需要再单独检查“同一行冲突”。

### 以 n = 4 为例

第 0 行可以尝试把皇后放在：

- 第 0 列
- 第 1 列
- 第 2 列
- 第 3 列

假设放在 `(0, 1)`：

- 标记第 1 列已使用
- 标记对应主对角线和副对角线已使用

然后递归去放第 1 行。

如果某一行发现没有合法位置可放，就说明当前分支走不通，撤销上一步，换别的位置继续尝试。

### 复杂度分析

- **时间复杂度：** 回溯问题通常为指数级，近似可写为 `O(n!)`
- **空间复杂度：** `O(n)`，主要来自递归栈和状态记录

## 代码

### C++

```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    vector<vector<string>> ans;
    vector<string> board;
    vector<bool> cols, diag1, diag2;

    void backtrack(int row, int n) {
        if (row == n) {
            ans.push_back(board);
            return;
        }

        for (int col = 0; col < n; col++) {
            int d1 = row - col + n - 1;
            int d2 = row + col;

            if (cols[col] || diag1[d1] || diag2[d2]) {
                continue;
            }

            board[row][col] = 'Q';
            cols[col] = diag1[d1] = diag2[d2] = true;

            backtrack(row + 1, n);

            board[row][col] = '.';
            cols[col] = diag1[d1] = diag2[d2] = false;
        }
    }

    vector<vector<string>> solveNQueens(int n) {
        board = vector<string>(n, string(n, '.'));
        cols = vector<bool>(n, false);
        diag1 = vector<bool>(2 * n - 1, false);
        diag2 = vector<bool>(2 * n - 1, false);

        backtrack(0, n);
        return ans;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def solveNQueens(self, n: int) -> List[List[str]]:
        ans = []
        board = [["."] * n for _ in range(n)]
        cols = [False] * n
        diag1 = [False] * (2 * n - 1)
        diag2 = [False] * (2 * n - 1)

        def backtrack(row: int) -> None:
            if row == n:
                ans.append(["".join(r) for r in board])
                return

            for col in range(n):
                d1 = row - col + n - 1
                d2 = row + col

                if cols[col] or diag1[d1] or diag2[d2]:
                    continue

                board[row][col] = "Q"
                cols[col] = diag1[d1] = diag2[d2] = True

                backtrack(row + 1)

                board[row][col] = "."
                cols[col] = diag1[d1] = diag2[d2] = False

        backtrack(0)
        return ans
```
