# 74. 搜索二维矩阵 (Search a 2D Matrix)

## 题目描述

给你一个满足以下条件的 `m x n` 整数矩阵 `matrix`：

1. 每行中的整数从左到右按升序排列
2. 每行的第一个整数大于前一行的最后一个整数

给你一个整数 `target`，如果 `target` 在矩阵中，返回 `true`，否则返回 `false`。

要求时间复杂度为 `O(log(m * n))`。

**示例 1：**

```text
输入：matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3
输出：true
```

**示例 2：**

```text
输入：matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 13
输出：false
```

**提示：**

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 100`
- `-10^4 <= matrix[i][j], target <= 10^4`

## 算法知识

### 二分查找

这题表面上是二维矩阵，实际上可以看成一个一维有序数组。

因为题目给了两个关键条件：

- 每一行内部是升序
- 下一行第一个元素大于上一行最后一个元素

所以如果把矩阵一行接一行展开，就会得到一个整体升序的一维数组。

例如：

```text
[
 [1, 3, 5, 7],
 [10,11,16,20],
 [23,30,34,60]
]
```

可以看成：

```text
[1,3,5,7,10,11,16,20,23,30,34,60]
```

既然整体有序，就可以直接二分查找。

## 解法：把二维矩阵映射成一维数组后二分（推荐）

### 思路

矩阵有 `m` 行、`n` 列。

如果把它看成一维数组，那么下标范围是：

```text
0 到 m * n - 1
```

我们在这个范围内做二分。

关键问题是：

> 一维下标 `mid` 怎么映射回二维矩阵位置？

答案是：

```text
row = mid / n
col = mid % n
```

也就是：

- `mid / n` 表示它在第几行
- `mid % n` 表示它在这一行的第几列

### 为什么这样映射是对的

因为每一行有 `n` 个元素。

假设 `n = 4`，一维下标和二维位置对应关系是：

```text
0 -> (0,0)
1 -> (0,1)
2 -> (0,2)
3 -> (0,3)
4 -> (1,0)
5 -> (1,1)
```

可以看到：

- 每过 `n` 个元素，行号就加 1
- 在一行内部，列号就是除以 `n` 后的余数

所以用：

```text
matrix[mid / n][mid % n]
```

就能拿到一维下标 `mid` 对应的矩阵元素。

### 二分过程

设：

```text
left = 0
right = m * n - 1
```

每次取：

```text
mid = left + (right - left) / 2
```

然后取出：

```text
num = matrix[mid / n][mid % n]
```

如果：

- `num == target`，直接返回 `true`
- `num < target`，说明目标在右边，令 `left = mid + 1`
- `num > target`，说明目标在左边，令 `right = mid - 1`

如果循环结束还没找到，就返回 `false`。

### 以示例 1 为例

```text
matrix =
[
 [1,3,5,7],
 [10,11,16,20],
 [23,30,34,60]
]
target = 3
```

把它看成一维数组：

```text
[1,3,5,7,10,11,16,20,23,30,34,60]
```

二分查找时，每个中点都通过：

```text
matrix[mid / 4][mid % 4]
```

找到对应的二维位置。

最终会找到数字 `3`，返回 `true`。

### 为什么不是逐行扫描

逐行扫描当然也能做，但最坏情况下要看完整个矩阵，时间复杂度是：

```text
O(m * n)
```

题目要求 `O(log(m * n))`，所以应该利用整体有序性，把整个矩阵当成一个有序数组来二分。

### 复杂度分析

- **时间复杂度：** `O(log(m * n))`，在所有元素范围内二分查找
- **空间复杂度：** `O(1)`，只使用常数个额外变量

## 代码

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m = matrix.size();
        int n = matrix[0].size();
        int left = 0;
        int right = m * n - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;
            int num = matrix[mid / n][mid % n];

            if (num == target) {
                return true;
            } else if (num < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }

        return false;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def searchMatrix(self, matrix: List[List[int]], target: int) -> bool:
        m, n = len(matrix), len(matrix[0])
        left, right = 0, m * n - 1

        while left <= right:
            mid = left + (right - left) // 2
            num = matrix[mid // n][mid % n]

            if num == target:
                return True
            elif num < target:
                left = mid + 1
            else:
                right = mid - 1

        return False
```
