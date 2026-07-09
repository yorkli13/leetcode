# 47. 全排列 II (Permutations II)

## 题目描述

给定一个可包含重复数字的序列 `nums`，按任意顺序返回所有不重复的全排列。

**示例 1：**

```text
输入：nums = [1,1,2]
输出：
[
  [1,1,2],
  [1,2,1],
  [2,1,1]
]
```

**示例 2：**

```text
输入：nums = [1,2,3]
输出：
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

**提示：**

- `1 <= nums.length <= 8`
- `-10 <= nums[i] <= 10`

## 算法知识

### 回溯

这题本质上仍然是排列问题，所以主方法还是回溯。

每一层都要决定：

- 当前这个位置放哪个数字

然后递归去填下一个位置，最后再撤销选择。

### 排序 + 同层去重

和第 46 题不同，这里数组里可能有重复元素。

例如：

```text
[1,1,2]
```

如果不做处理，那么：

- 第一个 `1` 放在第一位
- 第二个 `1` 放在第一位

这两种情况会生成重复排列。

为了避免这种重复，我们通常：

1. 先排序，让相同元素挨在一起
2. 在同一层递归中，如果当前数字和前一个数字相同，并且前一个数字在这一层还没被使用，就跳过当前数字

这就是排列题里的经典去重方式。

## 解法：回溯 + used 标记 + 同层去重（推荐）

### 思路

和第 46 题一样，我们仍然需要：

- `path`：当前排列
- `used[i]`：表示 `nums[i]` 是否已经被放进当前排列

不同点在于，这次要先排序：

```cpp
sort(nums.begin(), nums.end());
```

然后在遍历每个位置 `i` 时，除了判断：

- `used[i]` 是否已经为 `true`

还要额外判断：

```cpp
if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1])
```

如果这个条件成立，就跳过当前数字。

### 为什么 `!used[i - 1]` 时要跳过

这个条件非常关键。

它表示：

- 当前数字和前一个数字相同
- 前一个相同数字在这一层还没有被选中

那说明：

- 如果现在选当前这个数字
- 会和“选前一个相同数字”形成完全一样的分支

所以必须跳过，避免重复。

### 为什么是“前一个没用过才跳过”

因为如果：

```text
used[i - 1] == true
```

说明前一个相同数字已经在当前排列的前面位置被用了。

这时候当前这个相同数字是可以继续使用的，不会产生重复问题。

也就是说：

- 同层不能重复开头
- 但不同层可以继续使用相同数字

### 以 `[1,1,2]` 为例

排序后仍然是：

```text
[1,1,2]
```

第一层时：

- 可以选第一个 `1`
- 如果第一个 `1` 没选，就不能直接选第二个 `1`

因为这两个 `1` 作为“第一位”会生成重复排列。

但如果第一层已经选了第一个 `1`，进入下一层后：

- 第二个 `1` 是可以选的

这样才能得到合法排列：

```text
[1,1,2]
```

### 复杂度分析

- **时间复杂度：** 最坏情况下接近 `O(n * n!)`
- **空间复杂度：** `O(n)`，主要来自递归栈、`path` 和 `used`

虽然有去重，但数量级本质上仍然是排列枚举。

## 代码

### C++

```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    vector<vector<int>> ans;
    vector<int> path;
    vector<bool> used;

    void backtrack(vector<int>& nums) {
        if (path.size() == nums.size()) {
            ans.push_back(path);
            return;
        }

        for (int i = 0; i < nums.size(); i++) {
            if (used[i]) {
                continue;
            }

            if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) {
                continue;
            }

            path.push_back(nums[i]);
            used[i] = true;

            backtrack(nums);

            used[i] = false;
            path.pop_back();
        }
    }

    vector<vector<int>> permuteUnique(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        used.assign(nums.size(), false);
        backtrack(nums);
        return ans;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def permuteUnique(self, nums: List[int]) -> List[List[int]]:
        nums.sort()
        ans = []
        path = []
        used = [False] * len(nums)

        def backtrack() -> None:
            if len(path) == len(nums):
                ans.append(path[:])
                return

            for i in range(len(nums)):
                if used[i]:
                    continue

                if i > 0 and nums[i] == nums[i - 1] and not used[i - 1]:
                    continue

                path.append(nums[i])
                used[i] = True

                backtrack()

                used[i] = False
                path.pop()

        backtrack()
        return ans
```
