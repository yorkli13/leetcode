# 16. 最接近的三数之和 (3Sum Closest)

## 题目描述

给你一个长度为 `n` 的整数数组 `nums` 和一个目标值 `target`。请你从 `nums` 中选出三个整数，使它们的和与 `target` 最接近。

返回这三个数的和。

题目保证恰好存在一个解。

**示例 1：**

```text
输入：nums = [-1,2,1,-4], target = 1
输出：2
解释：与 target 最接近的和是 2（-1 + 2 + 1 = 2）。
```

**示例 2：**

```text
输入：nums = [0,0,0], target = 1
输出：0
```

**提示：**

- `3 <= nums.length <= 500`
- `-1000 <= nums[i] <= 1000`
- `-10^4 <= target <= 10^4`

## 算法知识

### 排序

排序后，数组变成有序状态，这样我们就能根据当前三数之和与目标值的大小关系，决定应该移动左指针还是右指针。

本题和第 15 题“三数之和”很像，区别在于：

- 第 15 题要找“恰好等于某个值”的组合
- 本题要找“最接近 target”的组合

### 双指针

固定第一个数后，在剩余区间中用两个指针 `left` 和 `right` 寻找另外两个数。

因为数组有序：

- 如果当前和小于 `target`，说明需要更大的和，就让 `left` 右移
- 如果当前和大于 `target`，说明需要更小的和，就让 `right` 左移

在移动过程中，持续更新“目前最接近 target 的答案”即可。

## 解法：排序 + 双指针（推荐）

### 思路

1. 先对数组排序。
2. 初始化答案 `ans` 为前三个数的和。
3. 枚举第一个数 `nums[i]`。
4. 对于每个 `i`，使用双指针 `left` 和 `right` 在右侧区间中寻找更接近 `target` 的三数之和。
5. 如果当前和比 `ans` 更接近 `target`，就更新答案。
6. 如果当前和恰好等于 `target`，可以直接返回，因为这已经是最优答案。

具体过程如下：

1. 排序数组
2. 枚举 `i` 作为第一个数
3. 令 `left = i + 1`，`right = n - 1`
4. 计算 `sum = nums[i] + nums[left] + nums[right]`
5. 比较 `abs(sum - target)` 和 `abs(ans - target)`，若更优则更新 `ans`
6. 如果 `sum < target`，移动 `left`
7. 如果 `sum > target`，移动 `right`
8. 如果 `sum == target`，直接返回 `target`

### 为什么这样做可行？

排序后，固定一个数，剩下的问题就是：

> 在一个有序数组中，找两个数，使得三数之和尽量接近目标值

双指针正适合解决这类“根据当前和的大小决定移动方向”的问题。

每次移动指针后，我们都检查当前和是否更接近目标，因此不会漏掉更优答案。因为题目保证唯一解，所以最终保留下来的答案就是正确结果。

### 复杂度分析

- **时间复杂度**：O(n^2)。排序是 O(n log n)，外层枚举 O(n)，内层双指针总共 O(n)，整体仍为 O(n^2)。
- **空间复杂度**：O(log n) 或 O(n)，取决于排序实现。若不计排序额外空间，可视为常数级额外变量。

## 代码

### C++

```cpp
#include <vector>
#include <algorithm>
#include <cstdlib>
using namespace std;

class Solution {
public:
    int threeSumClosest(vector<int>& nums, int target) {
        sort(nums.begin(), nums.end());
        int n = nums.size();
        int ans = nums[0] + nums[1] + nums[2];

        for (int i = 0; i < n - 2; i++) {
            int left = i + 1, right = n - 1;
            while (left < right) {
                int sum = nums[i] + nums[left] + nums[right];

                if (abs(sum - target) < abs(ans - target)) {
                    ans = sum;
                }

                if (sum < target) {
                    left++;
                } else if (sum > target) {
                    right--;
                } else {
                    return target;
                }
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
    def threeSumClosest(self, nums: List[int], target: int) -> int:
        nums.sort()
        n = len(nums)
        ans = nums[0] + nums[1] + nums[2]

        for i in range(n - 2):
            left, right = i + 1, n - 1
            while left < right:
                total = nums[i] + nums[left] + nums[right]

                if abs(total - target) < abs(ans - target):
                    ans = total

                if total < target:
                    left += 1
                elif total > target:
                    right -= 1
                else:
                    return target

        return ans
```
