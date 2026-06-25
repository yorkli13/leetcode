# 15. 三数之和 (3Sum)

## 题目描述

给你一个整数数组 `nums`，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足：

- `i != j`
- `i != k`
- `j != k`
- `nums[i] + nums[j] + nums[k] == 0`

请你返回所有和为 `0` 且**不重复**的三元组。

注意：答案中不可以包含重复的三元组。

**示例 1：**

```text
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
解释：
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0
不同的三元组是 [-1,0,1] 和 [-1,-1,2]
输出的顺序以及三元组的顺序并不重要。
```

**示例 2：**

```text
输入：nums = [0,1,1]
输出：[]
解释：不存在和为 0 的三元组。
```

**示例 3：**

```text
输入：nums = [0,0,0]
输出：[[0,0,0]]
```

**提示：**

- `3 <= nums.length <= 3000`
- `-10^5 <= nums[i] <= 10^5`

## 算法知识

### 排序

排序后，数组中的元素从小到大排列，这会带来两个好处：

1. 方便使用双指针根据和的大小移动指针
2. 方便去重，因为相同的数字会相邻出现

本题如果不排序，去重和查找都会更麻烦。

### 双指针

双指针通常用于有序数组中查找满足条件的组合。

在本题中，我们先固定第一个数 `nums[i]`，然后在区间 `[i+1, n-1]` 中用两个指针 `left` 和 `right` 找另外两个数，使得：

```text
nums[i] + nums[left] + nums[right] == 0
```

因为数组已经有序：

- 如果三数和太小，就让 `left` 右移
- 如果三数和太大，就让 `right` 左移

这样就能在线性时间内完成一次两数查找。

## 解法：排序 + 双指针（推荐）

### 思路

1. 先对数组排序。
2. 枚举第一个数 `nums[i]`。
3. 对于每个 `i`，使用双指针在右侧区间查找另外两个数。
4. 当三数和等于 `0` 时，加入答案。
5. 为了避免重复答案，需要对 `i`、`left`、`right` 都进行去重。

具体过程如下：

1. 排序后遍历 `i`
2. 如果 `nums[i]` 和前一个数相同，跳过，避免重复枚举第一个数
3. 令 `left = i + 1`，`right = n - 1`
4. 计算当前和 `sum = nums[i] + nums[left] + nums[right]`
5. 如果 `sum == 0`
   - 记录答案
   - 同时跳过 `left` 和 `right` 后面重复的值
6. 如果 `sum < 0`，说明和太小，移动 `left`
7. 如果 `sum > 0`，说明和太大，移动 `right`

### 为什么这样做可行？

排序后，固定一个数，相当于把问题变成：

> 在有序数组中找两个数，使它们的和等于某个目标值

这个子问题正是双指针最擅长解决的。

同时，因为数组有序，重复元素一定相邻，所以我们只要在枚举 `i` 和找到答案后跳过连续重复值，就能保证不会把同一个三元组加入多次。

### 复杂度分析

- **时间复杂度**：O(n^2)。排序是 O(n log n)，外层枚举 O(n)，内层双指针总共 O(n)，整体为 O(n^2)。
- **空间复杂度**：O(log n) 或 O(n)，取决于排序实现。若不计答案空间，可视为排序带来的额外空间。

## 代码

### C++

```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        vector<vector<int>> ans;
        int n = nums.size();

        for (int i = 0; i < n - 2; i++) {
            // 第一个数去重
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }

            int left = i + 1, right = n - 1;
            while (left < right) {
                int sum = nums[i] + nums[left] + nums[right];

                if (sum == 0) {
                    ans.push_back({nums[i], nums[left], nums[right]});

                    // 第二个数去重
                    while (left < right && nums[left] == nums[left + 1]) {
                        left++;
                    }
                    // 第三个数去重
                    while (left < right && nums[right] == nums[right - 1]) {
                        right--;
                    }

                    left++;
                    right--;
                } else if (sum < 0) {
                    left++;
                } else {
                    right--;
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
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        nums.sort()
        ans = []
        n = len(nums)

        for i in range(n - 2):
            # 第一个数去重
            if i > 0 and nums[i] == nums[i - 1]:
                continue

            left, right = i + 1, n - 1
            while left < right:
                total = nums[i] + nums[left] + nums[right]

                if total == 0:
                    ans.append([nums[i], nums[left], nums[right]])

                    # 第二个数去重
                    while left < right and nums[left] == nums[left + 1]:
                        left += 1
                    # 第三个数去重
                    while left < right and nums[right] == nums[right - 1]:
                        right -= 1

                    left += 1
                    right -= 1
                elif total < 0:
                    left += 1
                else:
                    right -= 1

        return ans
```
