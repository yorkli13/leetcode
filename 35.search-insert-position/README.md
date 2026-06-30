# 35. 搜索插入位置 (Search Insert Position)

## 题目描述

给定一个排序数组 `nums` 和一个目标值 `target`，在数组中找到目标值，并返回其下标。

如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

你必须使用时间复杂度为 O(log n) 的算法。

**示例 1：**

```text
输入：nums = [1,3,5,6], target = 5
输出：2
```

**示例 2：**

```text
输入：nums = [1,3,5,6], target = 2
输出：1
```

**示例 3：**

```text
输入：nums = [1,3,5,6], target = 7
输出：4
```

**提示：**

- `1 <= nums.length <= 10^4`
- `-10^4 <= nums[i] <= 10^4`
- `nums` 为无重复元素的升序排列数组
- `-10^4 <= target <= 10^4`

## 算法知识

### 二分查找

数组已经升序排列，并且题目要求时间复杂度 O(log n)，因此最适合使用二分查找。

二分查找的核心思想是：

1. 取中间位置 `mid`
2. 比较 `nums[mid]` 和 `target`
3. 根据大小关系丢弃一半区间

这样每次都把搜索范围缩小一半。

### 插入位置的含义

本题不仅要求“找到目标值”，还要求：

- 如果找不到，就返回应插入的位置

这个位置本质上就是：

> 数组中第一个大于等于 `target` 的位置

如果数组中所有数都小于 `target`，那么插入位置就是数组末尾，也就是 `nums.length`。

## 解法：二分查找（推荐）

### 思路

维护两个指针：

- `left`
- `right`

每次计算中点：

```text
mid = left + (right - left) / 2
```

然后分情况：

1. 如果 `nums[mid] == target`
   - 说明找到了目标值，直接返回 `mid`
2. 如果 `nums[mid] < target`
   - 说明目标值只能在右半边
   - 令 `left = mid + 1`
3. 如果 `nums[mid] > target`
   - 说明目标值只能在左半边，或者当前 `mid` 就是未来的插入位置
   - 令 `right = mid - 1`

当循环结束时，如果还没有找到目标值，那么：

- `left` 就会停在“第一个大于 `target` 的位置”
- 或者停在数组末尾后面

因此直接返回 `left` 即可。

### 为什么最后返回 `left`？

因为在二分过程中：

- 所有小于 `target` 的位置都会被排除到 `left` 左边
- 所有大于 `target` 的位置最终会留在 `left` 右边

所以循环结束时，`left` 正好指向：

- `target` 应该出现的位置

这就是插入位置。

### 复杂度分析

- **时间复杂度**：O(log n)，每次排除一半区间。
- **空间复杂度**：O(1)，只使用常数额外空间。

## 代码

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;

            if (nums[mid] == target) {
                return mid;
            } else if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }

        return left;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        left, right = 0, len(nums) - 1

        while left <= right:
            mid = left + (right - left) // 2

            if nums[mid] == target:
                return mid
            elif nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1

        return left
```
