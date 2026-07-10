# 54. 螺旋矩阵 (Spiral Matrix)

## 题目描述

给你一个 `m x n` 的矩阵 `matrix`，请按照顺时针螺旋顺序，返回矩阵中的所有元素。

**示例 1：**

```text
输入：
matrix =
[
 [1,2,3],
 [4,5,6],
 [7,8,9]
]
输出：[1,2,3,6,9,8,7,4,5]
```

**示例 2：**

```text
输入：
matrix =
[
  [1,2,3,4],
  [5,6,7,8],
  [9,10,11,12]
]
输出：[1,2,3,4,8,12,11,10,9,5,6,7]
```

**提示：**

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 10`
- `-100 <= matrix[i][j] <= 100`

## 算法知识

### 模拟

这题没有特别复杂的数据结构，本质上就是按照题目要求的顺序把矩阵一圈一圈地读出来。

这类题最自然的方法就是模拟：

1. 明确当前应该往哪个方向走
2. 每走完一条边，就收缩边界
3. 继续处理下一圈

### 边界控制

为了表示当前还没有被遍历的区域，我们维护四个边界：

- `top`：当前最上面一行
- `bottom`：当前最下面一行
- `left`：当前最左边一列
- `right`：当前最右边一列

每遍历完一条边，对应边界就向内收缩一格。

## 解法：四边界模拟螺旋遍历（推荐）

### 思路

我们用一个循环不断处理当前这一圈：

1. 从左到右遍历上边这一行
2. `top++`
3. 从上到下遍历右边这一列
4. `right--`
5. 从右到左遍历下边这一行
6. `bottom--`
7. 从下到上遍历左边这一列
8. `left++`

然后继续下一圈，直到边界交错为止。

### 为什么每一步后都要判断边界

因为矩阵不一定是正方形，也不一定每一圈都有完整的四条边。

例如只剩下一行时：

- 走完“从左到右”后
- 这一圈就已经结束了

如果不判断边界，还继续去走“从上到下”“从右到左”，就会重复读取元素。

所以每走完一条边并收缩边界后，都要检查：

```text
top <= bottom
left <= right
```

是否仍然成立。

### 以 3x3 为例

矩阵：

```text
1 2 3
4 5 6
7 8 9
```

第一圈：

1. 上边：`1 2 3`
2. 右边：`6 9`
3. 下边：`8 7`
4. 左边：`4`

第二圈只剩中间：

```text
5
```

最后答案就是：

```text
[1,2,3,6,9,8,7,4,5]
```

### 复杂度分析

- **时间复杂度：** `O(m * n)`，每个元素访问一次
- **空间复杂度：** `O(1)`，不算返回答案，只用了常数额外变量

## 代码

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector<int> ans;
        int top = 0, bottom = matrix.size() - 1;
        int left = 0, right = matrix[0].size() - 1;

        while (top <= bottom && left <= right) {
            for (int j = left; j <= right; j++) {
                ans.push_back(matrix[top][j]);
            }
            top++;

            for (int i = top; i <= bottom; i++) {
                ans.push_back(matrix[i][right]);
            }
            right--;

            if (top <= bottom) {
                for (int j = right; j >= left; j--) {
                    ans.push_back(matrix[bottom][j]);
                }
                bottom--;
            }

            if (left <= right) {
                for (int i = bottom; i >= top; i--) {
                    ans.push_back(matrix[i][left]);
                }
                left++;
            }
        }

        return ans;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def spiralOrder(self, matrix: List[List[int]]) -> List[int]:
        ans = []
        top, bottom = 0, len(matrix) - 1
        left, right = 0, len(matrix[0]) - 1

        while top <= bottom and left <= right:
            for j in range(left, right + 1):
                ans.append(matrix[top][j])
            top += 1

            for i in range(top, bottom + 1):
                ans.append(matrix[i][right])
            right -= 1

            if top <= bottom:
                for j in range(right, left - 1, -1):
                    ans.append(matrix[bottom][j])
                bottom -= 1

            if left <= right:
                for i in range(bottom, top - 1, -1):
                    ans.append(matrix[i][left])
                left += 1

        return ans
```
