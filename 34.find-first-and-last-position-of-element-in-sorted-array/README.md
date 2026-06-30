# 34. 在排序数组中查找元素的第一个和最后一个位置 (Find First and Last Position of Element in Sorted Array)

## 题目描述

给你一个按照非递减顺序排列的整数数组 `nums`，和一个目标值 `target`。请你找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 `target`，返回 `[-1, -1]`。

你必须设计并实现时间复杂度为 O(log n) 的算法解决此问题。

**示例 1：**

```text
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]
```

**示例 2：**

```text
输入：nums = [5,7,7,8,8,10], target = 6
输出：[-1,-1]
```

**示例 3：**

```text
输入：nums = [], target = 0
输出：[-1,-1]
```

**提示：**

- `0 <= nums.length <= 10^5`
- `-10^9 <= nums[i] <= 10^9`
- `nums` 是一个非递减数组
- `-10^9 <= target <= 10^9`

## 算法知识

### 二分查找

数组已经有序，并且题目要求时间复杂度为 O(log n)，这几乎直接提示我们使用二分查找。

不过本题不是单纯判断目标值是否存在，而是要找：

- 最左边的那个 `target`
- 最右边的那个 `target`

所以一次普通二分还不够，需要分别查左右边界。

### 边界二分

边界二分的核心是：

- 当遇到 `target` 时，不是立刻返回
- 而是继续往左或往右逼近边界

具体来说：

1. 查找左边界时
   - 即使 `nums[mid] == target`
   - 也要继续去左半边找，看前面还有没有 `target`

2. 查找右边界时
   - 即使 `nums[mid] == target`
   - 也要继续去右半边找，看后面还有没有 `target`

## 解法：两次二分查找边界（推荐）

### 思路

定义两个辅助函数：

1. `findLeft`
   - 找到第一个等于 `target` 的位置
2. `findRight`
   - 找到最后一个等于 `target` 的位置

#### 查找左边界

当 `nums[mid] >= target` 时，说明左边界可能在当前 `mid` 或更左边，所以收缩右边界：

```text
right = mid - 1
```

否则去右边找。

#### 查找右边界

当 `nums[mid] <= target` 时，说明右边界可能在当前 `mid` 或更右边，所以收缩左边界：

```text
left = mid + 1
```

否则去左边找。

最后：

- 如果左边界不存在，说明数组中没有 `target`，直接返回 `[-1,-1]`
- 否则返回 `[leftBound, rightBound]`

### 为什么这样做可行？

因为数组有序，所以所有等于 `target` 的元素一定连续出现。

因此：

- 第一次二分可以找到这段连续区间的最左端
- 第二次二分可以找到这段连续区间的最右端

这两个边界一旦确定，中间整段就都是目标值。

### 复杂度分析

- **时间复杂度**：O(log n)，两次二分查找，仍然是 O(log n)。
- **空间复杂度**：O(1)，只使用常数额外空间。

## 代码

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int findLeft(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;
        int ans = -1;

        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] >= target) {
                if (nums[mid] == target) {
                    ans = mid;
                }
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }

        return ans;
    }

    int findRight(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;
        int ans = -1;

        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] <= target) {
                if (nums[mid] == target) {
                    ans = mid;
                }
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }

        return ans;
    }

    vector<int> searchRange(vector<int>& nums, int target) {
        int leftBound = findLeft(nums, target);
        if (leftBound == -1) {
            return {-1, -1};
        }

        int rightBound = findRight(nums, target);
        return {leftBound, rightBound};
    }
};
```

### Python

```python
from typing import List


class Solution:
    def findLeft(self, nums: List[int], target: int) -> int:
        left, right = 0, len(nums) - 1
        ans = -1

        while left <= right:
            mid = left + (right - left) // 2
            if nums[mid] >= target:
                if nums[mid] == target:
                    ans = mid
                right = mid - 1
            else:
                left = mid + 1

        return ans

    def findRight(self, nums: List[int], target: int) -> int:
        left, right = 0, len(nums) - 1
        ans = -1

        while left <= right:
            mid = left + (right - left) // 2
            if nums[mid] <= target:
                if nums[mid] == target:
                    ans = mid
                left = mid + 1
            else:
                right = mid - 1

        return ans

    def searchRange(self, nums: List[int], target: int) -> List[int]:
        left_bound = self.findLeft(nums, target)
        if left_bound == -1:
            return [-1, -1]

        right_bound = self.findRight(nums, target)
        return [left_bound, right_bound]
```
