# 18. 四数之和 (4Sum)

## 题目描述

给你一个由 `n` 个整数组成的数组 `nums`，和一个目标值 `target`。请你找出并返回所有满足下列条件且**不重复**的四元组：

- `nums[a] + nums[b] + nums[c] + nums[d] == target`
- `a`、`b`、`c` 和 `d` 互不相同

你可以按任意顺序返回答案。

**示例 1：**

```text
输入：nums = [1,0,-1,0,-2,2], target = 0
输出：[[-2,-1,1,2],[-2,0,0,2],[-1,0,0,1]]
```

**示例 2：**

```text
输入：nums = [2,2,2,2,2], target = 8
输出：[[2,2,2,2]]
```

**提示：**

- `1 <= nums.length <= 200`
- `-10^9 <= nums[i] <= 10^9`
- `-10^9 <= target <= 10^9`

## 算法知识

### 排序

排序后，数组中的元素按从小到大排列，这有两个重要作用：

1. 方便使用双指针判断当前和偏大还是偏小
2. 方便去重，因为相同元素会集中在一起

本题和第 15 题“三数之和”非常相似，只是从“三个数”扩展成了“四个数”。

### 双指针

在固定前两个数之后，问题就转化成：

> 在有序数组中找两个数，使得四数之和等于目标值

这个子问题可以用双指针解决：

- 如果当前和太小，移动左指针
- 如果当前和太大，移动右指针
- 如果刚好等于目标值，记录答案并继续去重

## 解法：排序 + 双重枚举 + 双指针（推荐）

### 思路

1. 先对数组排序。
2. 枚举第一个数 `nums[i]`。
3. 再枚举第二个数 `nums[j]`。
4. 对于剩余区间 `[j+1, n-1]`，使用双指针 `left` 和 `right` 寻找另外两个数。
5. 找到和为 `target` 的四元组后加入答案，并跳过重复值。

具体流程如下：

1. 排序数组
2. 外层枚举 `i`
3. 如果 `nums[i]` 和前一个值相同，跳过，避免重复四元组
4. 内层枚举 `j`
5. 如果 `nums[j]` 和前一个值相同，也跳过
6. 令 `left = j + 1`，`right = n - 1`
7. 计算当前四数和
8. 如果和等于 `target`
   - 记录答案
   - 跳过 `left` 和 `right` 的重复值
9. 如果和小于 `target`，移动 `left`
10. 如果和大于 `target`，移动 `right`

### 为什么要用 `long long`？

因为题目中的数字范围很大：

- `nums[i]` 最小可到 `-10^9`
- 最大可到 `10^9`

四个数相加时，可能达到：

```text
4 * 10^9
```

这已经超过了 32 位 `int` 的表示范围，所以在 C++ 中计算和时最好使用 `long long`，避免整数溢出。

### 为什么这样做可行？

排序后，固定两个数，剩下就是标准的“两数之和”变体。双指针可以在线性时间里完成这一部分查找。

同时，由于相同元素相邻，我们可以在 `i`、`j`、`left`、`right` 四个层面分别去重，这样就能保证最终答案中不会出现重复四元组。

### 复杂度分析

- **时间复杂度**：O(n^3)。排序是 O(n log n)，两层枚举加一层双指针整体为 O(n^3)。
- **空间复杂度**：O(log n) 或 O(n)，取决于排序实现。若不计答案空间，只额外使用常数级变量。

## 代码

### C++

```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        sort(nums.begin(), nums.end());
        vector<vector<int>> ans;
        int n = nums.size();

        for (int i = 0; i < n - 3; i++) {
            // 第一个数去重
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }

            for (int j = i + 1; j < n - 2; j++) {
                // 第二个数去重
                if (j > i + 1 && nums[j] == nums[j - 1]) {
                    continue;
                }

                int left = j + 1, right = n - 1;
                while (left < right) {
                    long long sum = (long long)nums[i] + nums[j] + nums[left] + nums[right];

                    if (sum == target) {
                        ans.push_back({nums[i], nums[j], nums[left], nums[right]});

                        // 第三个数去重
                        while (left < right && nums[left] == nums[left + 1]) {
                            left++;
                        }
                        // 第四个数去重
                        while (left < right && nums[right] == nums[right - 1]) {
                            right--;
                        }

                        left++;
                        right--;
                    } else if (sum < target) {
                        left++;
                    } else {
                        right--;
                    }
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
    def fourSum(self, nums: List[int], target: int) -> List[List[int]]:
        nums.sort()
        ans = []
        n = len(nums)

        for i in range(n - 3):
            # 第一个数去重
            if i > 0 and nums[i] == nums[i - 1]:
                continue

            for j in range(i + 1, n - 2):
                # 第二个数去重
                if j > i + 1 and nums[j] == nums[j - 1]:
                    continue

                left, right = j + 1, n - 1
                while left < right:
                    total = nums[i] + nums[j] + nums[left] + nums[right]

                    if total == target:
                        ans.append([nums[i], nums[j], nums[left], nums[right]])

                        # 第三个数去重
                        while left < right and nums[left] == nums[left + 1]:
                            left += 1
                        # 第四个数去重
                        while left < right and nums[right] == nums[right - 1]:
                            right -= 1

                        left += 1
                        right -= 1
                    elif total < target:
                        left += 1
                    else:
                        right -= 1

        return ans
```
