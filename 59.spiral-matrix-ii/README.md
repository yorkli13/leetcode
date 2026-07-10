# 59. 螺旋矩阵 II (Spiral Matrix II)

## 题目描述

给你一个正整数 `n`，生成一个包含 `1` 到 `n^2` 所有元素，且元素按顺时针螺旋顺序排列的 `n x n` 正方形矩阵。

**示例 1：**

```text
输入：n = 3
输出：[[1,2,3],[8,9,4],[7,6,5]]
```

**示例 2：**

```text
输入：n = 1
输出：[[1]]
```

**提示：**

- `1 <= n <= 20`

## 算法知识

### 矩阵模拟

这题和第 54 题很像。

- 第 54 题是按螺旋顺序把矩阵“读出来”
- 这一题是按螺旋顺序把数字“填进去”

本质上都属于矩阵模拟题。

关键是维护当前这一圈还没有处理的边界：

- `top`：上边界
- `bottom`：下边界
- `left`：左边界
- `right`：右边界

每处理完一条边，就把对应边界向内收缩一格。

## 解法：四边界模拟顺时针填数（推荐）

### 思路

先创建一个 `n x n` 的矩阵，初始值都设成 0。

然后准备一个当前要填入的数字 `num`，从 1 开始不断递增。

接下来按一圈一圈的顺序填：

1. 从左到右填上边
2. 从上到下填右边
3. 从右到左填下边
4. 从下到上填左边

每填完一条边，就把对应边界向中间收缩。

### 为什么需要边界判断

虽然这是一个正方形矩阵，但在不断缩圈之后，最后可能只剩：

- 一行
- 一列
- 或者一个中心点

如果不做边界判断，就可能在某一圈里重复填同一个位置。

所以在填完上边和右边后，继续处理下边、左边之前，要判断：

```text
top <= bottom
left <= right
```

只有边界还合法，才继续填。

### 填数过程怎么理解

可以把它想成拿着一支笔，在矩阵外圈绕一圈：

- 先走最上面一行
- 再走最右边一列
- 再走最下面一行
- 再走最左边一列

走完这一圈以后，外圈就填完了，于是边界往里缩，继续填下一圈。

### 以 `n = 3` 为例

开始时矩阵是：

```text
0 0 0
0 0 0
0 0 0
```

第一圈填完后：

```text
1 2 3
8 0 4
7 6 5
```

此时边界收缩到中间，只剩中心位置 `(1,1)`，再填入 `9`：

```text
1 2 3
8 9 4
7 6 5
```

这就是最终答案。

### 复杂度分析

- **时间复杂度：** `O(n^2)`，因为矩阵中的每个位置都恰好填一次
- **空间复杂度：** `O(n^2)`，用于存储结果矩阵

## 代码

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        vector<vector<int>> matrix(n, vector<int>(n, 0));
        int top = 0, bottom = n - 1;
        int left = 0, right = n - 1;
        int num = 1;

        while (top <= bottom && left <= right) {
            for (int j = left; j <= right; j++) {
                matrix[top][j] = num++;
            }
            top++;

            for (int i = top; i <= bottom; i++) {
                matrix[i][right] = num++;
            }
            right--;

            if (top <= bottom) {
                for (int j = right; j >= left; j--) {
                    matrix[bottom][j] = num++;
                }
                bottom--;
            }

            if (left <= right) {
                for (int i = bottom; i >= top; i--) {
                    matrix[i][left] = num++;
                }
                left++;
            }
        }

        return matrix;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def generateMatrix(self, n: int) -> List[List[int]]:
        matrix = [[0] * n for _ in range(n)]
        top, bottom = 0, n - 1
        left, right = 0, n - 1
        num = 1

        while top <= bottom and left <= right:
            for j in range(left, right + 1):
                matrix[top][j] = num
                num += 1
            top += 1

            for i in range(top, bottom + 1):
                matrix[i][right] = num
                num += 1
            right -= 1

            if top <= bottom:
                for j in range(right, left - 1, -1):
                    matrix[bottom][j] = num
                    num += 1
                bottom -= 1

            if left <= right:
                for i in range(bottom, top - 1, -1):
                    matrix[i][left] = num
                    num += 1
                left += 1

        return matrix
```
