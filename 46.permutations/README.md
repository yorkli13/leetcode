# 46. 全排列 (Permutations)

## 题目描述

给定一个不含重复数字的数组 `nums`，返回其所有可能的全排列。你可以按任意顺序返回答案。

**示例 1：**

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

**示例 2：**

```text
输入：nums = [0,1]
输出：[[0,1],[1,0]]
```

**示例 3：**

```text
输入：nums = [1]
输出：[[1]]
```

**提示：**

- `1 <= nums.length <= 6`
- `-10 <= nums[i] <= 10`
- `nums` 中的所有整数互不相同

## 算法知识

### 回溯

这题要求我们列出“所有可能的排列”。

排列问题的特点是：

1. 每一步都要从还没用过的数字里选一个
2. 选完以后，剩余可选数字会变化
3. 当选满 `n` 个数字时，就得到一个完整答案

这种“尝试一个选择，递归深入，不行再撤回”的过程，就是典型的回溯。

### used 数组

和组合问题不同，全排列中：

- 数字的顺序不同，结果就不同

例如：

- `[1,2,3]`
- `[2,1,3]`

这是两个不同排列。

所以这里不能像组合题那样只靠 `start` 控制范围，而要记录：

- 哪些数字已经用过
- 哪些数字还没用过

这就是 `used` 数组的作用。

## 解法：回溯 + used 标记（推荐）

### 思路

我们维护两个东西：

1. `path`
   - 表示当前已经选出的排列前缀
2. `used`
   - `used[i] = true` 表示 `nums[i]` 已经被放进当前排列里

回溯过程如下：

1. 如果 `path` 的长度已经等于 `nums.size()`
   - 说明已经选出了一个完整排列
   - 把 `path` 加入答案
2. 否则遍历每个数字
   - 如果这个数字已经用过，就跳过
   - 如果还没用过，就把它加入 `path`
   - 同时把 `used[i]` 设为 `true`
   - 递归进入下一层
   - 回来后撤销这一步，恢复现场

### 为什么这里不用 start

在组合题里，我们常用 `start` 表示：

- 下一层只能从当前下标之后继续选

那样是为了避免重复组合。

但排列题不一样：

- 每一个位置都可以从“所有还没用过的数”里挑

例如第一位选了 `2` 之后，第二位仍然可以去选 `1`，也可以选 `3`。

所以这里不能用 `start` 限制下标，而必须用 `used` 去判断某个数是否已经被使用。

### 以 `[1,2,3]` 为例

开始时：

- `path = []`
- `used = [false, false, false]`

第一层：

- 可以选 `1`
- 也可以选 `2`
- 也可以选 `3`

假设先选 `1`：

- `path = [1]`

第二层：

- `1` 已经用过，不能再选
- 可以选 `2`
- 也可以选 `3`

如果再选 `2`：

- `path = [1,2]`

第三层：

- 只剩 `3`

于是得到：

```text
[1,2,3]
```

然后回溯，再尝试：

- `[1,3,2]`
- `[2,1,3]`
- ...

最终枚举出所有排列。

### 复杂度分析

- **时间复杂度：** `O(n * n!)`，共有 `n!` 个排列，每次复制答案需要 `O(n)`
- **空间复杂度：** `O(n)`，主要来自递归栈、`path` 和 `used`

## 代码

### C++

```cpp
#include <vector>
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

            path.push_back(nums[i]);
            used[i] = true;

            backtrack(nums);

            used[i] = false;
            path.pop_back();
        }
    }

    vector<vector<int>> permute(vector<int>& nums) {
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
    def permute(self, nums: List[int]) -> List[List[int]]:
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

                path.append(nums[i])
                used[i] = True

                backtrack()

                used[i] = False
                path.pop()

        backtrack()
        return ans
```
