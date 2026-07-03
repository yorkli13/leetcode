# 37. 解数独 (Sudoku Solver)

## 题目描述

编写一个程序，通过填充空格来解决数独问题。

数独的解法需要满足：

1. 每一行只能出现一次数字 `1-9`
2. 每一列只能出现一次数字 `1-9`
3. 每一个 `3 x 3` 的宫格内只能出现一次数字 `1-9`

数独中的空白格用 `'.'` 表示。

题目保证输入数独只有一个解。

你需要原地修改输入的 `board`，不要返回新的棋盘。

**示例 1：**

```text
输入：
board =
[["5","3",".",".","7",".",".",".","."]
,["6",".",".","1","9","5",".",".","."]
,[".","9","8",".",".",".",".","6","."]
,["8",".",".",".","6",".",".",".","3"]
,["4",".",".","8",".","3",".",".","1"]
,["7",".",".",".","2",".",".",".","6"]
,[".","6",".",".",".",".","2","8","."]
,[".",".",".","4","1","9",".",".","5"]
,[".",".",".",".","8",".",".","7","9"]]

输出：
[["5","3","4","6","7","8","9","1","2"]
,["6","7","2","1","9","5","3","4","8"]
,["1","9","8","3","4","2","5","6","7"]
,["8","5","9","7","6","1","4","2","3"]
,["4","2","6","8","5","3","7","9","1"]
,["7","1","3","9","2","4","8","5","6"]
,["9","6","1","5","3","7","2","8","4"]
,["2","8","7","4","1","9","6","3","5"]
,["3","4","5","2","8","6","1","7","9"]]
```

**提示：**

- `board.length == 9`
- `board[i].length == 9`
- `board[i][j]` 是数字 `1-9` 或 `'.'`
- 题目保证 `board` 有唯一解

## 算法知识

### 回溯

回溯是一种“尝试 + 撤销”的搜索方法。

它特别适合这种问题：

- 有很多空位要逐步决策
- 每一步有若干种候选选择
- 做出选择后，后面的合法范围会变小
- 如果走不通，就撤销当前选择，换另一种试法

数独正是这个模型：

1. 找一个空格
2. 尝试填入 `1-9`
3. 只保留当前合法的数字
4. 继续递归处理下一个空格
5. 如果后面失败，就把当前格恢复成 `'.'`

这种过程就叫回溯。

### 状态记录

如果每次尝试一个数字时，都重新扫描整行、整列、整宫格，会比较慢。

所以我们可以像第 36 题一样，提前维护三类状态：

- `rows[i][d]`：第 `i` 行是否已经用了数字 `d`
- `cols[j][d]`：第 `j` 列是否已经用了数字 `d`
- `boxes[box][d]`：第 `box` 个宫格是否已经用了数字 `d`

这样判断一个数字能不能填，只需要 O(1) 查询。

## 解法：回溯 + 行列宫状态记录（推荐）

### 思路

整个过程分两步：

1. 先遍历棋盘
   - 把已经出现的数字记录到行、列、宫格状态里
   - 同时把所有空格位置保存下来
2. 对空格列表做回溯
   - 依次处理第 `pos` 个空格
   - 尝试填入 `1-9`
   - 如果当前数字合法，就填进去并递归处理下一个空格
   - 如果后面失败，再撤销这一步

当 `pos` 走到空格列表末尾时，说明所有空格都已经成功填完，数独解出来了。

### 宫格编号怎么计算

某个位置 `(i, j)` 属于哪个 `3 x 3` 宫格，可以用：

```text
box = (i / 3) * 3 + (j / 3)
```

这样 9 个小宫格就会被编号成 `0 ~ 8`。

### 为什么必须回溯

因为你在某个空格里填的数字，可能当前看起来合法，但会影响后面的空格。

例如：

- 当前位置填 `5`，现在不冲突
- 但递归下去后，后面某个空格会发现没有任何合法数字可填

这就说明当前这一步虽然“局部合法”，但不是正确答案的一部分，于是必须撤销，再换一个数字继续试。

这就是回溯的核心。

### 复杂度分析

- **时间复杂度：** 最坏情况下是指数级，通常记作 `O(9^m)`，其中 `m` 是空格数量
- **空间复杂度：** `O(m)`，主要来自递归栈；状态数组本身大小固定，也可以看成常数空间

## 代码

### C++

```cpp
#include <vector>
#include <utility>
using namespace std;

class Solution {
public:
    void solveSudoku(vector<vector<char>>& board) {
        vector<vector<bool>> rows(9, vector<bool>(10, false));
        vector<vector<bool>> cols(9, vector<bool>(10, false));
        vector<vector<bool>> boxes(9, vector<bool>(10, false));
        vector<pair<int, int>> spaces;

        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                if (board[i][j] == '.') {
                    spaces.push_back({i, j});
                } else {
                    int num = board[i][j] - '0';
                    int box = (i / 3) * 3 + (j / 3);
                    rows[i][num] = true;
                    cols[j][num] = true;
                    boxes[box][num] = true;
                }
            }
        }

        backtrack(board, spaces, 0, rows, cols, boxes);
    }

    bool backtrack(vector<vector<char>>& board,
                   vector<pair<int, int>>& spaces,
                   int pos,
                   vector<vector<bool>>& rows,
                   vector<vector<bool>>& cols,
                   vector<vector<bool>>& boxes) {
        if (pos == spaces.size()) {
            return true;
        }

        auto [i, j] = spaces[pos];
        int box = (i / 3) * 3 + (j / 3);

        for (int num = 1; num <= 9; num++) {
            if (rows[i][num] || cols[j][num] || boxes[box][num]) {
                continue;
            }

            board[i][j] = num + '0';
            rows[i][num] = true;
            cols[j][num] = true;
            boxes[box][num] = true;

            if (backtrack(board, spaces, pos + 1, rows, cols, boxes)) {
                return true;
            }

            board[i][j] = '.';
            rows[i][num] = false;
            cols[j][num] = false;
            boxes[box][num] = false;
        }

        return false;
    }
};
```

### Python

```python
from typing import List, Tuple


class Solution:
    def solveSudoku(self, board: List[List[str]]) -> None:
        rows = [[False] * 10 for _ in range(9)]
        cols = [[False] * 10 for _ in range(9)]
        boxes = [[False] * 10 for _ in range(9)]
        spaces: List[Tuple[int, int]] = []

        for i in range(9):
            for j in range(9):
                if board[i][j] == ".":
                    spaces.append((i, j))
                else:
                    num = int(board[i][j])
                    box = (i // 3) * 3 + (j // 3)
                    rows[i][num] = True
                    cols[j][num] = True
                    boxes[box][num] = True

        def backtrack(pos: int) -> bool:
            if pos == len(spaces):
                return True

            i, j = spaces[pos]
            box = (i // 3) * 3 + (j // 3)

            for num in range(1, 10):
                if rows[i][num] or cols[j][num] or boxes[box][num]:
                    continue

                board[i][j] = str(num)
                rows[i][num] = True
                cols[j][num] = True
                boxes[box][num] = True

                if backtrack(pos + 1):
                    return True

                board[i][j] = "."
                rows[i][num] = False
                cols[j][num] = False
                boxes[box][num] = False

            return False

        backtrack(0)
```
