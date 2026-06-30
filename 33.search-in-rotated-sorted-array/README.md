# 33. 搜索旋转排序数组 (Search in Rotated Sorted Array)

## 题目描述

整数数组 `nums` 按升序排列，数组中的值互不相同。

在传递给函数之前，`nums` 在预先未知的某个下标 `k` 上进行了旋转，使数组变为：

```text
[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]
```

例如：

```text
[0,1,2,4,5,6,7]
```

在下标 `3` 处旋转后可以变成：

```text
[4,5,6,7,0,1,2]
```

给你旋转后的数组 `nums` 和一个整数 `target`，如果 `target` 在数组中存在，返回它的下标，否则返回 `-1`。

要求时间复杂度为 O(log n)。

**示例 1：**

```text
输入：nums = [4,5,6,7,0,1,2], target = 0
输出：4
```

**示例 2：**

```text
输入：nums = [4,5,6,7,0,1,2], target = 3
输出：-1
```

**示例 3：**

```text
输入：nums = [1], target = 0
输出：-1
```

**提示：**

- `1 <= nums.length <= 5000`
- `-10^4 <= nums[i] <= 10^4`
- `nums` 中的每个值都独一无二
- `nums` 保证会在某个下标上旋转
- `-10^4 <= target <= 10^4`

## 算法知识

### 二分查找

二分查找适用于有序数组，每次通过比较中间值来排除掉一半搜索区间，因此时间复杂度是 O(log n)。

本题虽然数组被旋转了，但并没有完全失去有序性：

- 在任意时刻的搜索区间内，至少有一半仍然是有序的

这正是本题可以继续用二分查找的关键。

### 旋转数组的结构

例如数组：

```text
[4,5,6,7,0,1,2]
```

虽然整体不是完全升序，但可以看作由两段有序区间拼起来：

- 左段：`[4,5,6,7]`
- 右段：`[0,1,2]`

在二分过程中，我们每次都可以判断：

- `mid` 落在哪个有序区间里
- `target` 是否可能在这个有序区间内

然后决定保留哪一半继续搜索。

## 解法：二分查找（推荐）

### 思路

维护两个指针：

- `left`
- `right`

每次取中点：

```text
mid = left + (right - left) / 2
```

如果 `nums[mid] == target`，直接返回 `mid`。

否则分情况讨论：

1. 如果 `nums[left] <= nums[mid]`
   - 说明左半段 `[left, mid]` 是有序的
   - 如果 `target` 落在这个有序区间内：
     - 缩小到左半边
   - 否则：
     - 去右半边找

2. 否则
   - 说明右半段 `[mid, right]` 是有序的
   - 如果 `target` 落在这个有序区间内：
     - 缩小到右半边
   - 否则：
     - 去左半边找

重复直到找到目标或区间为空。

### 为什么这样做可行？

因为数组中没有重复元素，所以在任意时刻：

- 要么左半段有序
- 要么右半段有序

这意味着我们总能确定一段“正常升序”的区间。

一旦知道某一半有序，就可以判断 `target` 是否落在这个区间的值范围内：

- 如果在，就保留这一半
- 如果不在，就排除这一半

这样每次都能安全丢掉一半区间，仍然保持 O(log n)。

### 复杂度分析

- **时间复杂度**：O(log n)，每次排除一半搜索区间。
- **空间复杂度**：O(1)，只使用常数额外空间。

## 代码

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;

            if (nums[mid] == target) {
                return mid;
            }

            // 左半段有序
            if (nums[left] <= nums[mid]) {
                if (nums[left] <= target && target < nums[mid]) {
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            }
            // 右半段有序
            else {
                if (nums[mid] < target && target <= nums[right]) {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
        }

        return -1;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left, right = 0, len(nums) - 1

        while left <= right:
            mid = left + (right - left) // 2

            if nums[mid] == target:
                return mid

            # 左半段有序
            if nums[left] <= nums[mid]:
                if nums[left] <= target < nums[mid]:
                    right = mid - 1
                else:
                    left = mid + 1
            # 右半段有序
            else:
                if nums[mid] < target <= nums[right]:
                    left = mid + 1
                else:
                    right = mid - 1

        return -1
```
