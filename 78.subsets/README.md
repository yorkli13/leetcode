# 78. 子集 (Subsets)

## 题目描述

给你一个整数数组 `nums`，数组中的元素互不相同。

请返回该数组所有可能的子集，也就是幂集。

答案不能包含重复的子集，你可以按任意顺序返回答案。

**示例 1：**

```text
输入：nums = [1,2,3]
输出：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

**示例 2：**

```text
输入：nums = [0]
输出：[[],[0]]
```

**提示：**

- `1 <= nums.length <= 10`
- `-10 <= nums[i] <= 10`
- `nums` 中的所有元素互不相同

## 算法知识

### 回溯

这题是经典的子集枚举问题。

和第 77 题“组合”很像，但区别在于：

- 第 77 题只收集长度为 `k` 的路径
- 第 78 题每一种长度都要收集，包括空集

所以在回溯过程中，每到一个节点，都可以把当前路径加入答案。

这也是子集题和组合题一个很重要的区别。

## 解法：回溯枚举所有选择路径（推荐）

### 思路

我们维护一个当前路径：

```text
path
```

表示当前已经选中的元素。

再维护一个起始位置：

```text
start
```

表示下一次可以从 `nums` 的哪个下标开始选择。

每次进入递归时，先把当前 `path` 加入答案。

然后从 `start` 开始，依次尝试把后面的元素加入 `path`。

### 为什么一进入递归就加入答案

因为子集不要求固定长度。

例如：

```text
nums = [1,2,3]
```

合法子集包括：

```text
[]
[1]
[1,2]
[1,2,3]
```

也包括：

```text
[2]
[2,3]
[3]
```

所以当前路径无论长度是多少，都是一个合法子集。

因此一进入递归，就可以先执行：

```text
ans.push_back(path)
```

### 为什么下一层从 `i + 1` 开始

子集和组合一样，不关心顺序。

如果已经选择了 `nums[i]`，那么后面只能继续选择它右边的元素。

所以递归下一层写成：

```text
backtrack(i + 1)
```

这样可以避免出现重复子集，比如：

```text
[1,2]
[2,1]
```

这两个在子集里其实是同一个集合。

### 回溯的三个动作

对于每个下标 `i`：

1. 选择 `nums[i]`

```text
path.push_back(nums[i])
```

2. 递归处理后面的元素

```text
backtrack(i + 1)
```

3. 撤销选择

```text
path.pop_back()
```

撤销选择以后，才能继续尝试同一层的下一个元素。

### 以 `[1,2,3]` 为例

递归过程可以理解成：

```text
先收集 []

选择 1，收集 [1]
  选择 2，收集 [1,2]
    选择 3，收集 [1,2,3]
  撤销 2，选择 3，收集 [1,3]

撤销 1，选择 2，收集 [2]
  选择 3，收集 [2,3]

撤销 2，选择 3，收集 [3]
```

最终得到所有子集。

### 子集数量为什么是 `2^n`

数组里的每个元素都有两种选择：

- 选它
- 不选它

一共有 `n` 个元素，所以总子集数量是：

```text
2^n
```

这也是这题时间复杂度一定会是指数级的原因。

### 复杂度分析

- **时间复杂度：** `O(n * 2^n)`，一共有 `2^n` 个子集，每个子集复制到答案中最多需要 `O(n)`
- **空间复杂度：** `O(n)`，递归路径深度最多为 `n`，不计算返回答案空间

## 代码

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> ans;
        vector<int> path;

        backtrack(0, nums, path, ans);
        return ans;
    }

private:
    void backtrack(int start, vector<int>& nums, vector<int>& path, vector<vector<int>>& ans) {
        ans.push_back(path);

        for (int i = start; i < nums.size(); i++) {
            path.push_back(nums[i]);
            backtrack(i + 1, nums, path, ans);
            path.pop_back();
        }
    }
};
```

### Python

```python
from typing import List


class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        ans = []
        path = []

        def backtrack(start: int) -> None:
            ans.append(path[:])

            for i in range(start, len(nums)):
                path.append(nums[i])
                backtrack(i + 1)
                path.pop()

        backtrack(0)
        return ans
```
