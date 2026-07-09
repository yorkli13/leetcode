# 52. N 皇后 II (N-Queens II)

## 题目描述

给你一个整数 `n`，返回 `n` 皇后问题不同解法的数量。

皇后可以攻击与之处在同一行、同一列、同一斜线上的棋子。

**示例 1：**

```text
输入：n = 4
输出：2
```

**示例 2：**

```text
输入：n = 1
输出：1
```

**提示：**

- `1 <= n <= 9`

## 算法知识

### 回溯

这题和第 51 题本质上是同一个问题：

- 每一行放一个皇后
- 保证列和对角线不冲突

不同的是：

- 第 51 题要返回所有棋盘
- 第 52 题只需要返回解的数量

所以依然适合用回溯，只不过当找到一个完整解时：

- 不再保存棋盘
- 只把计数加一

### 列和对角线状态记录

为了快速判断某个位置 `(row, col)` 能不能放皇后，我们仍然记录：

1. `cols[col]`
   - 第 `col` 列是否已有皇后
2. `diag1[row - col + n - 1]`
   - 主对角线是否已有皇后
3. `diag2[row + col]`
   - 副对角线是否已有皇后

这样每次判断是否合法只需要 `O(1)`。

## 解法：按行回溯 + 计数（推荐）

### 思路

我们从第 0 行开始，一行一行放皇后。

对于当前行 `row`，尝试每一列 `col`：

1. 如果当前列冲突，跳过
2. 如果当前主对角线冲突，跳过
3. 如果当前副对角线冲突，跳过
4. 否则就在这个位置放一个皇后
5. 标记列和对角线已使用
6. 递归处理下一行
7. 回来后撤销标记

当：

```text
row == n
```

说明前面 `n` 行都已经成功放好皇后，这就是一个完整解，于是把答案计数加一。

### 为什么这题不需要真的保存棋盘

因为题目只问：

```text
一共有多少种不同解法
```

并不要求输出每一种棋盘布局。

所以我们不需要像第 51 题那样维护 `board`，只需要维护冲突状态和计数器即可。

这样代码会更简洁一些。

### 对角线编号回顾

对于位置 `(row, col)`：

#### 主对角线

```text
row - col
```

相同的位置在同一条主对角线上。

由于可能为负数，所以加偏移量：

```text
row - col + n - 1
```

#### 副对角线

```text
row + col
```

相同的位置在同一条副对角线上。

### 以 n = 4 为例

从第 0 行开始：

- 尝试把皇后放在第 0、1、2、3 列

每放一个位置，就递归去下一行继续尝试。

如果某一行没有合法列可放，说明当前分支失败，回溯。

最终每当成功放满 4 行，就说明找到一个完整解，计数加一。

### 复杂度分析

- **时间复杂度：** 回溯问题通常近似为 `O(n!)`
- **空间复杂度：** `O(n)`，主要来自递归栈和状态数组

## 代码

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int ans = 0;
    vector<bool> cols, diag1, diag2;

    void backtrack(int row, int n) {
        if (row == n) {
            ans++;
            return;
        }

        for (int col = 0; col < n; col++) {
            int d1 = row - col + n - 1;
            int d2 = row + col;

            if (cols[col] || diag1[d1] || diag2[d2]) {
                continue;
            }

            cols[col] = diag1[d1] = diag2[d2] = true;
            backtrack(row + 1, n);
            cols[col] = diag1[d1] = diag2[d2] = false;
        }
    }

    int totalNQueens(int n) {
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
class Solution:
    def totalNQueens(self, n: int) -> int:
        cols = [False] * n
        diag1 = [False] * (2 * n - 1)
        diag2 = [False] * (2 * n - 1)
        ans = 0

        def backtrack(row: int) -> None:
            nonlocal ans
            if row == n:
                ans += 1
                return

            for col in range(n):
                d1 = row - col + n - 1
                d2 = row + col

                if cols[col] or diag1[d1] or diag2[d2]:
                    continue

                cols[col] = diag1[d1] = diag2[d2] = True
                backtrack(row + 1)
                cols[col] = diag1[d1] = diag2[d2] = False

        backtrack(0)
        return ans
```
