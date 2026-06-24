# 4. 寻找两个正序数组的中位数

## 题目描述

给定两个大小分别为 `m` 和 `n` 的正序（从小到大）数组 `nums1` 和 `nums2`。请你找出并返回这两个正序数组的 **中位数**。

算法的时间复杂度应该为 O(log (m+n))。

**示例 1：**

```
输入：nums1 = [1,3], nums2 = [2]
输出：2.00000
解释：合并数组 = [1,2,3]，中位数 2
```

**示例 2：**

```
输入：nums1 = [1,2], nums2 = [3,4]
输出：2.50000
解释：合并数组 = [1,2,3,4]，中位数 (2 + 3) / 2 = 2.5
```

**示例 3：**

```
输入：nums1 = [0,0], nums2 = [0,0]
输出：0.00000
```

**示例 4：**

```
输入：nums1 = [], nums2 = [1]
输出：1.00000
```

**示例 5：**

```
输入：nums1 = [2], nums2 = []
输出：2.00000
```

**提示：**

- `nums1.length == m`
- `nums2.length == n`
- `0 <= m <= 1000`
- `0 <= n <= 1000`
- `1 <= m + n <= 2000`
- `-10^6 <= nums1[i], nums2[i] <= 10^6`

## 算法知识

### 二分查找

二分查找（Binary Search）是在**有序**数据集合中快速定位目标值的算法，每次将搜索范围缩小一半，时间复杂度 O(log n)。

**核心思想**：
1. 确定搜索区间的左右边界 `[left, right]`。
2. 取中间位置 `mid = left + (right - left) / 2`（防溢出写法）。
3. 根据目标值与 `mid` 处值的大小关系，舍弃一半的搜索区间。
4. 重复直到找到目标或区间为空。

**本题中的应用**：不是在数组中找一个具体值，而是在短数组上二分查找一个**分割位置**，使得两个数组被分割成左右两部分，满足左边所有数 ≤ 右边所有数，且左右数量相等（或多一个）。

### 中位数的定义

对于一个长度为 `n` 的有序数组：
- 如果 `n` 为奇数，中位数是第 `(n+1)/2` 个元素（0-indexed: `n/2`）。
- 如果 `n` 为偶数，中位数是第 `n/2` 个和第 `n/2 + 1` 个元素的平均值。

本题将两个有序数组合并看待，在合并后的有序数组中找到中位数。

## 解法一：二分查找（最优解）

### 思路

在两个有序数组中找到一条**分割线**，使得：

1. 分割线左边的元素个数 = 分割线右边的元素个数（或左边多一个，当总长度为奇数时）。
2. 分割线左边的所有元素 ≤ 分割线右边的所有元素。

设短数组为 `nums1`（长数组为 `nums2`），在短数组上二分查找分割位置 `i`，则长数组的分割位置 `j = totalLeft - i`。

分割线左边的最大值为 `max(nums1[i-1], nums2[j-1])`，右边的最小值为 `min(nums1[i], nums2[j])`。

**为什么在短数组上二分？** 保证 `j` 的索引不越界，因为 `j = totalLeft - i`，当 `i` 在短数组范围内变化时，`j` 始终合法。

### 边界处理

- 当 `i = 0` 时，`nums1` 左部分为空，`nums1LeftMax = -∞`。
- 当 `i = m` 时，`nums1` 右部分为空，`nums1RightMin = +∞`。
- 当 `j = 0` 时，`nums2` 左部分为空，`nums2LeftMax = -∞`。
- 当 `j = n` 时，`nums2` 右部分为空，`nums2RightMin = +∞`。

### 复杂度分析

- **时间复杂度**：O(log(min(m, n)))。在较短的数组上进行二分查找。
- **空间复杂度**：O(1)。只使用了常数个变量。

## 解法二：双指针归并（备选）

### 思路

模拟合并两个有序数组的过程，但不需要真正合并完，只需遍历到中位数位置即可。

使用两个指针分别遍历两个数组，比较当前元素大小，较小的进入合并序列。当合并到第 `(m+n+1)/2` 个位置时，即可得到中位数。

### 复杂度分析

- **时间复杂度**：O(m+n)。需要遍历到中位数位置。
- **空间复杂度**：O(1)。

> 注意：该解法不符合题目要求的 O(log(m+n)) 时间复杂度，仅作为思维拓展。

## 代码

### C++（二分查找）

```cpp
#include <vector>
#include <algorithm>
#include <climits>
using namespace std;

class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        // 保证 nums1 是较短的数组
        if (nums1.size() > nums2.size()) {
            return findMedianSortedArrays(nums2, nums1);
        }

        int m = nums1.size(), n = nums2.size();
        int totalLeft = (m + n + 1) / 2; // 分割线左边元素个数

        int left = 0, right = m;
        while (left < right) {
            int i = left + (right - left + 1) / 2;
            int j = totalLeft - i;
            if (nums1[i - 1] > nums2[j]) {
                // i 太大，需要左移
                right = i - 1;
            } else {
                left = i;
            }
        }

        int i = left, j = totalLeft - i;

        int nums1LeftMax = (i == 0) ? INT_MIN : nums1[i - 1];
        int nums1RightMin = (i == m) ? INT_MAX : nums1[i];
        int nums2LeftMax = (j == 0) ? INT_MIN : nums2[j - 1];
        int nums2RightMin = (j == n) ? INT_MAX : nums2[j];

        if ((m + n) % 2 == 1) {
            return max(nums1LeftMax, nums2LeftMax);
        } else {
            return (max(nums1LeftMax, nums2LeftMax) +
                    min(nums1RightMin, nums2RightMin)) / 2.0;
        }
    }
};
```

### Python（二分查找）

```python
from typing import List

class Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        # 保证 nums1 是较短的数组
        if len(nums1) > len(nums2):
            return self.findMedianSortedArrays(nums2, nums1)

        m, n = len(nums1), len(nums2)
        total_left = (m + n + 1) // 2  # 分割线左边元素个数

        left, right = 0, m
        while left < right:
            i = left + (right - left + 1) // 2
            j = total_left - i
            if nums1[i - 1] > nums2[j]:
                # i 太大，需要左移
                right = i - 1
            else:
                left = i

        i, j = left, total_left - left

        import sys
        nums1_left_max = nums1[i - 1] if i > 0 else -sys.maxsize
        nums1_right_min = nums1[i] if i < m else sys.maxsize
        nums2_left_max = nums2[j - 1] if j > 0 else -sys.maxsize
        nums2_right_min = nums2[j] if j < n else sys.maxsize

        if (m + n) % 2 == 1:
            return max(nums1_left_max, nums2_left_max)
        else:
            return (max(nums1_left_max, nums2_left_max) +
                    min(nums1_right_min, nums2_right_min)) / 2.0
```

### Python（双指针归并，仅供理解）

```python
from typing import List

class Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        m, n = len(nums1), len(nums2)
        total = m + n
        # 需要找到第 k 小的元素
        k = total // 2

        i = j = 0
        # prev 记录上一个值，curr 记录当前值
        prev = curr = 0

        for _ in range(k + 1):
            prev = curr
            if i < m and (j >= n or nums1[i] <= nums2[j]):
                curr = nums1[i]
                i += 1
            else:
                curr = nums2[j]
                j += 1

        if total % 2 == 0:
            return (prev + curr) / 2.0
        else:
            return float(curr)
```
