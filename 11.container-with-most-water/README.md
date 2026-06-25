# 11. 盛最多水的容器 (Container With Most Water)

## 题目描述

给定一个长度为 `n` 的整数数组 `height`。有 `n` 条垂线，第 `i` 条线的两个端点分别是 `(i, 0)` 和 `(i, height[i])`。

找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水，并返回可以储存的最大水量。

**说明：** 你不能倾斜容器。

**示例 1：**

```text
输入：height = [1,8,6,2,5,4,8,3,7]
输出：49
解释：选择下标 1 和 8，对应高度分别为 8 和 7，宽度为 7，
面积 = min(8, 7) * 7 = 49。
```

**示例 2：**

```text
输入：height = [1,1]
输出：1
```

**提示：**

- `n == height.length`
- `2 <= n <= 10^5`
- `0 <= height[i] <= 10^4`

## 算法知识

### 双指针

双指针常用于数组中“从两端向中间收缩”的问题。通常会维护两个位置，然后根据某种贪心规则移动其中一个指针，从而避免暴力枚举所有组合。

本题如果暴力枚举任意两条线，时间复杂度是 O(n^2)，当 `n = 10^5` 时会超时。双指针可以把复杂度优化到 O(n)。

### 容器面积公式

两条线形成的容器面积由两部分决定：

1. 宽度：两条线的横坐标差，即 `right - left`
2. 高度：两条线中较短的那一条，即 `min(height[left], height[right])`

所以面积公式是：

```text
area = min(height[left], height[right]) * (right - left)
```

注意，容器能装多少水，不取决于更高的那条线，而取决于**较短的那条线**，因为水面高度最多只能到短板那里。

## 解法：双指针（推荐）

### 思路

使用两个指针：

- `left` 指向最左边
- `right` 指向最右边

每一步都计算当前两条线形成的面积，并更新最大值。

关键在于如何移动指针：

1. 如果 `height[left] < height[right]`
   - 当前容器的高度由 `height[left]` 决定
   - 此时如果移动右指针，宽度会变小，而短板仍然可能是左边，面积不可能变大
   - 所以应该移动左指针，尝试寻找更高的短板

2. 如果 `height[left] >= height[right]`
   - 当前容器的高度由 `height[right]` 决定
   - 同理，应该移动右指针

也就是说：**每次都移动较短的那根线**。

### 为什么移动短板是对的？

假设当前：

- 宽度是 `w`
- 左边高度是 `h1`
- 右边高度是 `h2`
- 且 `h1 < h2`

当前面积是：

```text
h1 * w
```

如果这时移动右指针：

- 宽度一定变小
- 新高度最多还是受 `h1` 限制，甚至可能更小

所以面积不可能超过当前值。

只有移动左指针，才有机会找到比 `h1` 更高的线，从而让短板变高，尽管宽度变小，但仍然可能得到更大的面积。

### 复杂度分析

- **时间复杂度**：O(n)，两个指针各最多移动 `n` 次。
- **空间复杂度**：O(1)，只使用常数额外空间。

## 代码

### C++

```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int maxArea(vector<int>& height) {
        int left = 0, right = height.size() - 1;
        int ans = 0;

        while (left < right) {
            int h = min(height[left], height[right]);
            int w = right - left;
            ans = max(ans, h * w);

            // 移动短板，才有机会让容器高度变大
            if (height[left] < height[right]) {
                left++;
            } else {
                right--;
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
    def maxArea(self, height: List[int]) -> int:
        left, right = 0, len(height) - 1
        ans = 0

        while left < right:
            h = min(height[left], height[right])
            w = right - left
            ans = max(ans, h * w)

            # 移动短板，才有机会让容器高度变大
            if height[left] < height[right]:
                left += 1
            else:
                right -= 1

        return ans
```
