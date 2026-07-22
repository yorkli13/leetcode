# 79. 单词搜索 (Word Search)

## 题目描述

给定一个 `m x n` 的字符网格 `board` 和一个字符串 `word`。

如果 `word` 存在于网格中，返回 `true`；否则返回 `false`。

单词必须按照字母顺序，通过相邻的单元格组成。

相邻单元格是指水平或垂直方向相邻的格子。

同一个单元格内的字母不允许被重复使用。

**示例 1：**

```text
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
输出：true
```

**示例 2：**

```text
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "SEE"
输出：true
```

**示例 3：**

```text
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCB"
输出：false
解释：同一个格子不能重复使用
```

**提示：**

- `m == board.length`
- `n == board[i].length`
- `1 <= m, n <= 6`
- `1 <= word.length <= 15`
- `board` 和 `word` 仅由大小写英文字母组成

## 算法知识

### 回溯

这题是二维网格上的回溯搜索。

和第 77、78 题那种“从数组中选数”的回溯不同，这里每一步是在矩阵里上下左右移动。

核心规则有三个：

1. 当前格子的字符必须和 `word` 当前字符相同
2. 下一步只能走上下左右四个方向
3. 同一个格子不能在同一条路径中重复使用

所以我们需要在搜索时临时标记当前格子已经访问过，递归回来后再恢复。

## 解法：从每个格子开始 DFS 回溯（推荐）

### 思路

我们遍历矩阵中的每个格子，把它当作单词的起点尝试搜索。

如果从某个格子出发，可以完整匹配 `word`，就返回 `true`。

否则继续尝试下一个格子。

### DFS 函数含义

定义一个函数：

```text
dfs(i, j, index)
```

表示：

> 当前走到位置 `(i, j)`，想要匹配 `word[index]` 这个字符。

如果当前字符匹配成功，就继续向四个方向搜索 `word[index + 1]`。

### 递归结束条件

如果：

```text
index == word.length()
```

说明前面的所有字符都已经匹配完成，直接返回 `true`。

这是成功出口。

### 失败条件

如果出现以下任意情况，就说明当前路径不合法：

1. 坐标越界
2. 当前格子已经访问过
3. 当前格子的字符不等于 `word[index]`

这时直接返回 `false`。

### 如何避免重复使用同一个格子

当一个格子被当前路径使用时，先把它临时改成一个特殊字符，例如：

```text
'#'
```

表示它已经被访问过。

递归搜索四个方向结束后，再把它恢复成原来的字符。

这个过程就是回溯：

```text
标记选择
递归搜索
撤销标记
```

### 为什么递归后要恢复字符

因为这个格子不能在“同一条路径”中重复使用，但它可以在“另一条路径”中被使用。

例如从 `(0,0)` 出发失败了，后面从 `(0,1)` 出发时，之前用过的格子应该重新变成可用状态。

所以必须在递归结束后恢复原字符。

### 以示例 1 为例

```text
board =
[
 [A,B,C,E],
 [S,F,C,S],
 [A,D,E,E]
]
word = "ABCCED"
```

可以找到路径：

```text
A -> B -> C -> C -> E -> D
```

对应位置大致是：

```text
(0,0) -> (0,1) -> (0,2) -> (1,2) -> (2,2) -> (2,1)
```

所以返回 `true`。

### 为什么要从每个格子都尝试

因为题目没有说单词一定从左上角开始。

任何一个格子都可能是单词的第一个字符。

所以需要遍历所有格子，只要有一个起点能匹配成功，就可以返回 `true`。

### 复杂度分析

- **时间复杂度：** `O(m * n * 4^L)`，其中 `L = word.length()`；每个格子都可能作为起点，每一步最多向四个方向搜索
- **空间复杂度：** `O(L)`，递归深度最多为单词长度

## 代码

### C++

```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        int m = board.size();
        int n = board[0].size();

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (dfs(board, word, i, j, 0)) {
                    return true;
                }
            }
        }

        return false;
    }

private:
    bool dfs(vector<vector<char>>& board, string& word, int i, int j, int index) {
        if (index == word.size()) {
            return true;
        }

        int m = board.size();
        int n = board[0].size();

        if (i < 0 || i >= m || j < 0 || j >= n || board[i][j] != word[index]) {
            return false;
        }

        char old = board[i][j];
        board[i][j] = '#';

        bool found =
            dfs(board, word, i + 1, j, index + 1) ||
            dfs(board, word, i - 1, j, index + 1) ||
            dfs(board, word, i, j + 1, index + 1) ||
            dfs(board, word, i, j - 1, index + 1);

        board[i][j] = old;
        return found;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def exist(self, board: List[List[str]], word: str) -> bool:
        m, n = len(board), len(board[0])

        def dfs(i: int, j: int, index: int) -> bool:
            if index == len(word):
                return True

            if i < 0 or i >= m or j < 0 or j >= n or board[i][j] != word[index]:
                return False

            old = board[i][j]
            board[i][j] = "#"

            found = (
                dfs(i + 1, j, index + 1)
                or dfs(i - 1, j, index + 1)
                or dfs(i, j + 1, index + 1)
                or dfs(i, j - 1, index + 1)
            )

            board[i][j] = old
            return found

        for i in range(m):
            for j in range(n):
                if dfs(i, j, 0):
                    return True

        return False
```
