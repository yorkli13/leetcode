# 48. 旋转图像 (Rotate Image)

## 题目描述

给定一个 `n x n` 的二维矩阵 `matrix`，表示一个图像。请你将图像顺时针旋转 `90` 度。

你必须在原地旋转图像，这意味着你需要直接修改输入的二维矩阵。不要使用另一个矩阵来完成旋转。

**示例 1：**

```text
输入：
matrix =
[
  [1,2,3],
  [4,5,6],
  [7,8,9]
]

输出：
[
  [7,4,1],
  [8,5,2],
  [9,6,3]
]
```

**示例 2：**

```text
输入：
matrix =
[
  [5,1,9,11],
  [2,4,8,10],
  [13,3,6,7],
  [15,14,12,16]
]

输出：
[
  [15,13,2,5],
  [14,3,4,1],
  [12,6,8,9],
  [16,7,10,11]
]
```

**提示：**

- `n == matrix.length == matrix[i].length`
- `1 <= n <= 20`
- `-1000 <= matrix[i][j] <= 1000`

## 算法知识

### 矩阵变换

这题表面上是在“旋转矩阵”，但真正好用的思路是：

不要直接去硬转四个角、四条边，而是把旋转拆成两个更简单的矩阵操作。

对于顺时针旋转 `90` 度，有一个很经典的等价变换：

1. 先沿主对角线转置
2. 再把每一行反转

这样做之后，结果就等价于顺时针旋转 `90` 度。

### 转置

主对角线转置的意思是：

- `matrix[i][j]`
- 和 `matrix[j][i]`

交换位置。

例如：

```text
1 2 3
4 5 6
7 8 9
```

转置后变成：

```text
1 4 7
2 5 8
3 6 9
```

## 解法：转置 + 每行反转（推荐）

### 思路

如果我们要把矩阵顺时针旋转 `90` 度，可以分成两步：

#### 第一步：主对角线转置

对所有：

```text
i < j
```

的位置，交换：

```text
matrix[i][j] 和 matrix[j][i]
```

这样矩阵的行列关系会被交换。

#### 第二步：反转每一行

转置之后，再把每一行左右翻转。

这样就能把“转置后的结果”变成“顺时针旋转 `90` 度后的结果”。

### 为什么这样做等价于顺时针旋转

原来一个位置：

```text
(i, j)
```

顺时针旋转 `90` 度后，会到：

```text
(j, n - 1 - i)
```

而：

1. 转置会把 `(i, j)` 变成 `(j, i)`
2. 再左右翻转一整行，会把 `(j, i)` 变成 `(j, n - 1 - i)`

刚好和目标位置完全一致。

### 以 3x3 矩阵为例

原矩阵：

```text
1 2 3
4 5 6
7 8 9
```

先转置：

```text
1 4 7
2 5 8
3 6 9
```

再把每一行反转：

```text
7 4 1
8 5 2
9 6 3
```

这正是顺时针旋转 `90` 度后的结果。

### 为什么转置时只需要 `i < j`

因为：

- `matrix[i][j]` 和 `matrix[j][i]` 是成对交换的

如果把整个矩阵所有位置都交换一遍，就会交换两次，又回去了。

所以只需要处理主对角线的一侧，也就是：

```text
i < j
```

### 复杂度分析

- **时间复杂度：** `O(n^2)`，矩阵中的元素都会被访问
- **空间复杂度：** `O(1)`，原地修改，没有使用额外矩阵

## 代码

### C++

```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int n = matrix.size();

        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                swap(matrix[i][j], matrix[j][i]);
            }
        }

        for (int i = 0; i < n; i++) {
            reverse(matrix[i].begin(), matrix[i].end());
        }
    }
};
```

### Python

```python
from typing import List


class Solution:
    def rotate(self, matrix: List[List[int]]) -> None:
        n = len(matrix)

        for i in range(n):
            for j in range(i + 1, n):
                matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]

        for i in range(n):
            matrix[i].reverse()
```
