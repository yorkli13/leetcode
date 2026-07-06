# 41. 缺失的第一个正数 (First Missing Positive)

## 题目描述

给你一个未排序的整数数组 `nums`，请你找出其中没有出现的最小的正整数。

你必须设计并实现时间复杂度为 `O(n)` 且只使用常数级额外空间的算法。

**示例 1：**

```text
输入：nums = [1,2,0]
输出：3
```

**示例 2：**

```text
输入：nums = [3,4,-1,1]
输出：2
```

**示例 3：**

```text
输入：nums = [7,8,9,11,12]
输出：1
```

**提示：**

- `1 <= nums.length <= 10^5`
- `-2^31 <= nums[i] <= 2^31 - 1`

## 算法知识

### 原地哈希 / 下标映射

这题最难的地方在于两个限制：

1. 时间复杂度要 `O(n)`
2. 额外空间只能是 `O(1)`

如果用哈希表当然容易做，但空间不符合要求。

所以我们要把“数组本身”当成一个哈希结构来用。

核心想法是：

- 数字 `1` 应该放在下标 `0`
- 数字 `2` 应该放在下标 `1`
- 数字 `3` 应该放在下标 `2`

也就是：

```text
值 x 应该尽量放到下标 x - 1 的位置
```

这样当数组整理完后，如果某个位置没有放对，就能立刻知道缺的是哪个正数。

### 为什么只关心 `1 ~ n`

设数组长度为 `n`。

那么最小缺失正数只可能在：

```text
1 ~ n + 1
```

原因是：

- 如果 `1 ~ n` 都出现了
- 那答案就是 `n + 1`

所以：

- 小于等于 `0` 的数没有意义
- 大于 `n` 的数对找最小缺失正数也没有直接帮助

## 解法：把数字放回它应该在的位置（推荐）

### 思路

我们遍历数组，对每个位置 `i`：

只要当前数字 `nums[i]` 满足下面三个条件，就把它交换到它应该去的位置：

1. `nums[i]` 是正数
2. `nums[i] <= n`
3. `nums[i]` 还没有放在正确位置上

正确位置是：

```text
nums[i] 应该去下标 nums[i] - 1
```

所以我们不断交换：

```text
swap(nums[i], nums[nums[i] - 1])
```

直到当前这个位置再也不能继续交换为止。

然后再从头扫描一遍数组：

- 如果 `nums[0] != 1`，说明缺的是 `1`
- 如果 `nums[1] != 2`，说明缺的是 `2`
- ...

第一个不满足：

```text
nums[i] == i + 1
```

的位置，答案就是：

```text
i + 1
```

如果整个数组都满足，那么说明 `1 ~ n` 全都出现了，答案就是：

```text
n + 1
```

### 以 `[3,4,-1,1]` 为例

数组长度 `n = 4`

目标是尽量整理成：

```text
[1,2,3,4]
```

开始处理：

1. `nums[0] = 3`
   - 3 应该去下标 2
   - 交换后数组变成 `[-1,4,3,1]`
2. `nums[1] = 4`
   - 4 应该去下标 3
   - 交换后数组变成 `[-1,1,3,4]`
3. `nums[1] = 1`
   - 1 应该去下标 0
   - 交换后数组变成 `[1,-1,3,4]`

整理完成后再扫描：

- 下标 0 是 1，正确
- 下标 1 不是 2

所以答案就是 `2`。

### 为什么要用 `while` 而不是 `if`

因为一次交换后，当前位置 `i` 上可能来了一个新的数字，它也许还需要继续被放到正确位置。

所以不能只交换一次，而要：

- 只要还能放，就继续放

这就是 `while` 的意义。

### 复杂度分析

- **时间复杂度：** `O(n)`，每个数字最多被交换到正确位置一次左右
- **空间复杂度：** `O(1)`，只用了常数额外变量

## 代码

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        int n = nums.size();

        for (int i = 0; i < n; i++) {
            while (nums[i] > 0 &&
                   nums[i] <= n &&
                   nums[nums[i] - 1] != nums[i]) {
                swap(nums[i], nums[nums[i] - 1]);
            }
        }

        for (int i = 0; i < n; i++) {
            if (nums[i] != i + 1) {
                return i + 1;
            }
        }

        return n + 1;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def firstMissingPositive(self, nums: List[int]) -> int:
        n = len(nums)

        for i in range(n):
            while 1 <= nums[i] <= n and nums[nums[i] - 1] != nums[i]:
                correct_index = nums[i] - 1
                nums[i], nums[correct_index] = nums[correct_index], nums[i]

        for i in range(n):
            if nums[i] != i + 1:
                return i + 1

        return n + 1
```
