# 73. 矩阵置零 (Set Matrix Zeroes)

## 题目描述

给定一个 `m x n` 的矩阵 `matrix`，如果某个元素为 `0`，就将它所在的整行和整列都设为 `0`。

请你原地修改矩阵。

**示例 1：**

```text
输入：matrix = [[1,1,1],[1,0,1],[1,1,1]]
输出：[[1,0,1],[0,0,0],[1,0,1]]
```

**示例 2：**

```text
输入：matrix = [[0,1,2,0],[3,4,5,2],[1,3,1,5]]
输出：[[0,0,0,0],[0,4,5,0],[0,3,1,0]]
```

**提示：**

- `m == matrix.length`
- `n == matrix[0].length`
- `1 <= m, n <= 200`
- `-2^31 <= matrix[i][j] <= 2^31 - 1`

## 算法知识

### 原地标记

最直接的想法是额外开两个数组：

```text
rows[i] 表示第 i 行是否需要置零
cols[j] 表示第 j 列是否需要置零
```

这样做很容易理解，但需要 `O(m + n)` 的额外空间。

这题的进阶要求是尽量使用常数空间，所以可以把矩阵自己的第一行和第一列当作标记数组。

也就是说：

- 如果第 `i` 行需要置零，就标记 `matrix[i][0] = 0`
- 如果第 `j` 列需要置零，就标记 `matrix[0][j] = 0`

这样就不用额外开数组了。

## 解法：用第一行和第一列作为标记（推荐）

### 思路

因为第一行和第一列要被用来当标记，所以要先单独记录：

- 第一行原本是否有 0
- 第一列原本是否有 0

否则后面做标记时，会破坏第一行、第一列原本的信息。

### 第一步：记录第一行和第一列是否需要置零

先扫描第一行：

```text
firstRowZero = 第一行是否出现过 0
```

再扫描第一列：

```text
firstColZero = 第一列是否出现过 0
```

这两个变量后面专门用来决定第一行和第一列最终要不要整体置零。

### 第二步：用第一行和第一列记录其他位置的 0

从 `(1, 1)` 开始扫描矩阵。

如果发现：

```text
matrix[i][j] == 0
```

说明：

- 第 `i` 行最终要置零
- 第 `j` 列最终要置零

于是标记：

```text
matrix[i][0] = 0
matrix[0][j] = 0
```

### 第三步：根据标记更新内部区域

再次从 `(1, 1)` 开始扫描。

对于每个位置 `(i, j)`，如果：

```text
matrix[i][0] == 0
或者
matrix[0][j] == 0
```

说明它所在的行或列需要置零，所以：

```text
matrix[i][j] = 0
```

### 第四步：处理第一行和第一列

最后根据一开始记录的两个变量处理边界：

- 如果 `firstRowZero == true`，就把第一行全部置零
- 如果 `firstColZero == true`，就把第一列全部置零

### 为什么第一行第一列要最后处理

因为它们在中间阶段承担了“标记数组”的作用。

如果太早把第一行或第一列全部置零，那么标记信息就会混乱，导致一些本来不该置零的位置也被置零。

所以顺序必须是：

1. 先记录第一行、第一列原始状态
2. 再用第一行、第一列做标记
3. 先更新内部区域
4. 最后处理第一行、第一列

### 以示例 1 为例

```text
matrix =
[
 [1,1,1],
 [1,0,1],
 [1,1,1]
]
```

发现中间位置 `(1,1)` 是 0，于是标记：

```text
matrix[1][0] = 0
matrix[0][1] = 0
```

矩阵变成：

```text
[
 [1,0,1],
 [0,0,1],
 [1,1,1]
]
```

再根据标记把内部区域置零，最后得到：

```text
[
 [1,0,1],
 [0,0,0],
 [1,0,1]
]
```

### 复杂度分析

- **时间复杂度：** `O(m * n)`，需要扫描矩阵常数次
- **空间复杂度：** `O(1)`，只使用两个额外布尔变量

## 代码

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();

        bool firstRowZero = false;
        bool firstColZero = false;

        for (int j = 0; j < n; j++) {
            if (matrix[0][j] == 0) {
                firstRowZero = true;
                break;
            }
        }

        for (int i = 0; i < m; i++) {
            if (matrix[i][0] == 0) {
                firstColZero = true;
                break;
            }
        }

        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][j] == 0) {
                    matrix[i][0] = 0;
                    matrix[0][j] = 0;
                }
            }
        }

        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                    matrix[i][j] = 0;
                }
            }
        }

        if (firstRowZero) {
            for (int j = 0; j < n; j++) {
                matrix[0][j] = 0;
            }
        }

        if (firstColZero) {
            for (int i = 0; i < m; i++) {
                matrix[i][0] = 0;
            }
        }
    }
};
```

### Python

```python
from typing import List


class Solution:
    def setZeroes(self, matrix: List[List[int]]) -> None:
        m, n = len(matrix), len(matrix[0])

        first_row_zero = any(matrix[0][j] == 0 for j in range(n))
        first_col_zero = any(matrix[i][0] == 0 for i in range(m))

        for i in range(1, m):
            for j in range(1, n):
                if matrix[i][j] == 0:
                    matrix[i][0] = 0
                    matrix[0][j] = 0

        for i in range(1, m):
            for j in range(1, n):
                if matrix[i][0] == 0 or matrix[0][j] == 0:
                    matrix[i][j] = 0

        if first_row_zero:
            for j in range(n):
                matrix[0][j] = 0

        if first_col_zero:
            for i in range(m):
                matrix[i][0] = 0
```
