# 42. 接雨水 (Trapping Rain Water)

## 题目描述

给定 `n` 个非负整数，表示每个宽度为 `1` 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

**示例 1：**

```text
输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
输出：6
解释：这组高度图一共可以接 6 个单位的雨水。
```

**示例 2：**

```text
输入：height = [4,2,0,3,2,5]
输出：9
```

**提示：**

- `n == height.length`
- `1 <= n <= 2 * 10^4`
- `0 <= height[i] <= 10^5`

## 算法知识

### 接水公式

对于某一个位置 `i`，它上面能接多少水，取决于两边最高柱子里较矮的那一边。

也就是说：

```text
water[i] = min(左边最高, 右边最高) - 当前高度
```

如果这个值小于等于 `0`，就说明这个位置接不到水。

所以这题的本质是：

1. 知道当前位置左边最高是多少
2. 知道当前位置右边最高是多少
3. 用较小的那个减去当前高度

### 双指针

如果每个位置都单独去找左边最高和右边最高，复杂度会很高。

更高效的办法是用双指针：

- 一个指针从左往右走
- 一个指针从右往左走
- 同时维护：
  - `leftMax`：左边目前见过的最高柱子
  - `rightMax`：右边目前见过的最高柱子

这样就能在线性时间里算出答案。

## 解法：双指针 + 左右最高边界（推荐）

### 思路

我们用两个指针：

- `left` 指向最左边
- `right` 指向最右边

并维护两个变量：

- `leftMax`：从左边走到当前位置时见过的最高柱子
- `rightMax`：从右边走到当前位置时见过的最高柱子

每一步比较：

- `height[left]`
- `height[right]`

如果左边更低，说明当前左侧位置能接多少水，只取决于 `leftMax`。

因为右边既然还有一个更高或至少不低于左边的挡板，那么左边这一格的短板一定在左边。

这时：

1. 更新 `leftMax`
2. 如果 `height[left] < leftMax`
   - 就可以接水 `leftMax - height[left]`
3. 然后 `left++`

反过来，如果右边更低，就处理右边这一格：

1. 更新 `rightMax`
2. 如果 `height[right] < rightMax`
   - 就可以接水 `rightMax - height[right]`
3. 然后 `right--`

### 为什么“谁矮动谁”

这是这题最关键的一点。

假设：

```text
height[left] < height[right]
```

那么当前位置 `left` 能接多少水，决定它的是：

- 左边最高 `leftMax`
- 右边至少有一个 `height[right]` 作为挡板，而且它已经不低于当前左柱

也就是说，此时我们已经可以确定左边这一格的答案，不需要再等更多右边信息。

所以可以放心处理左边。

同理：

- 如果 `height[right] <= height[left]`
- 就可以先处理右边

### 以示例一为例

```text
height = [0,1,0,2,1,0,1,3,2,1,2,1]
```

比如走到某一步时：

- 左边最高是 `2`
- 当前高度是 `0`

那这个位置能接的水就是：

```text
2 - 0 = 2
```

整段过程用双指针一路累加，就能得到总答案 `6`。

### 复杂度分析

- **时间复杂度：** `O(n)`，每个位置最多被访问一次
- **空间复杂度：** `O(1)`，只用了几个变量

## 代码

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int trap(vector<int>& height) {
        int left = 0, right = height.size() - 1;
        int leftMax = 0, rightMax = 0;
        int ans = 0;

        while (left < right) {
            if (height[left] < height[right]) {
                if (height[left] >= leftMax) {
                    leftMax = height[left];
                } else {
                    ans += leftMax - height[left];
                }
                left++;
            } else {
                if (height[right] >= rightMax) {
                    rightMax = height[right];
                } else {
                    ans += rightMax - height[right];
                }
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
    def trap(self, height: List[int]) -> int:
        left, right = 0, len(height) - 1
        left_max, right_max = 0, 0
        ans = 0

        while left < right:
            if height[left] < height[right]:
                if height[left] >= left_max:
                    left_max = height[left]
                else:
                    ans += left_max - height[left]
                left += 1
            else:
                if height[right] >= right_max:
                    right_max = height[right]
                else:
                    ans += right_max - height[right]
                right -= 1

        return ans
```
