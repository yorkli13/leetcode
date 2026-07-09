# 53. 最大子数组和 (Maximum Subarray)

## 题目描述

给你一个整数数组 `nums`，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**示例 1：**

```text
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6
```

**示例 2：**

```text
输入：nums = [1]
输出：1
```

**示例 3：**

```text
输入：nums = [5,4,-1,7,8]
输出：23
```

**提示：**

- `1 <= nums.length <= 10^5`
- `-10^4 <= nums[i] <= 10^4`

## 算法知识

### 动态规划

这题最经典的想法是：

定义：

```text
dp[i] = 以 nums[i] 结尾的最大连续子数组和
```

为什么这样定义有用？

因为如果我们已经知道：

- 以 `nums[i - 1]` 结尾的最大和是多少

那么面对 `nums[i]` 时，只需要决定：

1. 是把前面的子数组接过来
2. 还是从 `nums[i]` 自己重新开始

这就是一个很标准的动态规划状态转移。

### Kadane 算法

这题的最优解通常叫 Kadane 算法。

它其实就是把上面的 DP 压缩成常数空间：

- `current`：当前“以当前位置结尾”的最大和
- `ans`：全局最大和

所以它本质仍然是 DP，只不过不需要真的开一个数组。

## 解法：动态规划空间优化（Kadane，推荐）

### 思路

设：

```text
current = 以当前位置结尾的最大子数组和
```

当我们看到 `nums[i]` 时，有两种选择：

1. 单独从 `nums[i]` 开始
2. 把它接到前面的最大连续和后面

所以状态转移就是：

```text
current = max(nums[i], current + nums[i])
```

然后再用：

```text
ans = max(ans, current)
```

不断更新全局最大值。

### 为什么这样转移是对的

因为“以 `i` 结尾”的最大子数组，只可能有两种来源：

1. 只包含 `nums[i]` 自己
2. 来自“以 `i-1` 结尾”的最大子数组，再加上 `nums[i]`

不会有第三种情况。

所以只需要在这两种里取最大值即可。

### 什么时候要重新开始

如果前面的 `current` 已经是负数，那么：

```text
current + nums[i]
```

只会拖累当前值。

这时候不如直接从 `nums[i]` 重新开始。

所以你也可以把它理解成：

- 前面的和如果对我有帮助，就接上
- 前面的和如果在拖后腿，就丢掉

### 以示例 1 为例

```text
nums = [-2,1,-3,4,-1,2,1,-5,4]
```

遍历过程大致是：

- 到 `1` 时，重新开始更好
- 到 `4` 时，重新开始更好
- 然后继续接上 `-1, 2, 1`

最终得到最大连续子数组：

```text
[4,-1,2,1]
```

和为：

```text
6
```

### 复杂度分析

- **时间复杂度：** `O(n)`，只遍历数组一次
- **空间复杂度：** `O(1)`，只用了常数额外变量

## 代码

### C++

```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int current = nums[0];
        int ans = nums[0];

        for (int i = 1; i < nums.size(); i++) {
            current = max(nums[i], current + nums[i]);
            ans = max(ans, current);
        }

        return ans;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        current = nums[0]
        ans = nums[0]

        for i in range(1, len(nums)):
            current = max(nums[i], current + nums[i])
            ans = max(ans, current)

        return ans
```
