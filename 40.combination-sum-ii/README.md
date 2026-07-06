# 40. 组合总和 II (Combination Sum II)

## 题目描述

给定一个候选人编号的集合 `candidates` 和一个目标数 `target`，找出 `candidates` 中所有可以使数字和为 `target` 的组合。

`candidates` 中的每个数字在每个组合中只能使用一次。

注意：解集不能包含重复的组合。

你可以按任意顺序返回这些组合。

**示例 1：**

```text
输入：candidates = [10,1,2,7,6,1,5], target = 8
输出：
[
  [1,1,6],
  [1,2,5],
  [1,7],
  [2,6]
]
```

**示例 2：**

```text
输入：candidates = [2,5,2,1,2], target = 5
输出：
[
  [1,2,2],
  [5]
]
```

**提示：**

- `1 <= candidates.length <= 100`
- `1 <= candidates[i] <= 50`
- `1 <= target <= 30`

## 算法知识

### 回溯

这题依然是“找所有组合”的问题，所以仍然适合用回溯。

回溯的核心还是：

1. 尝试选择一个数
2. 进入下一层搜索
3. 如果不合适，就撤销这次选择

但是这题比第 39 题多了两个限制：

- 每个数字只能用一次
- 原数组里可能有重复数字

所以除了基本回溯，还必须额外处理“去重”。

### 排序 + 同层去重

如果数组里有重复数字，比如：

```text
[1, 1, 2]
```

那么在同一层递归里，如果我们先选第一个 `1`，又选第二个 `1` 作为起点，就会生成重复组合。

解决方法是：

1. 先排序，让相同数字挨在一起
2. 在同一层递归中，如果当前数字和前一个数字相同，就跳过

这就叫“同层去重”。

## 解法：排序 + 回溯 + 同层去重（推荐）

### 思路

先对 `candidates` 排序。

然后定义回溯函数，参数包括：

- `start`：这一层从哪个下标开始选
- `remain`：还差多少才能凑到 `target`
- `path`：当前组合

回溯过程如下：

1. 如果 `remain == 0`
   - 说明当前组合正好满足要求
   - 把它加入答案
2. 如果 `remain < 0`
   - 说明已经超了
   - 直接返回
3. 从 `start` 开始枚举每个位置 `i`
   - 如果当前数字和前一个数字相同，并且它们处在同一层递归
   - 就跳过当前数字，避免重复组合
   - 否则把当前数字加入 `path`
   - 递归进入下一层
   - 回来后撤销选择

### 为什么递归传 `i + 1`

这里和第 39 题不一样。

第 39 题允许同一个数字重复使用，所以递归时传的是 `i`。

但这题每个数字只能用一次，所以当前下标 `i` 这个元素一旦用了，下一层就只能从后一个位置继续选：

```text
backtrack(i + 1, remain - candidates[i])
```

这就是“每个元素最多使用一次”的体现。

### 为什么要同层去重

看这个例子：

```text
candidates = [1,1,2]
```

如果在同一层里：

- 先用第一个 `1` 开头
- 再用第二个 `1` 开头

得到的后续组合结构会重复。

所以需要这句判断：

```cpp
if (i > start && candidates[i] == candidates[i - 1]) {
    continue;
}
```

意思是：

- 如果当前数字和前一个数字相同
- 并且当前数字不是这一层的第一个可选位置

那它会导致和前一个数字重复的分支，所以直接跳过。

### 复杂度分析

- **时间复杂度：** 与搜索树规模有关，通常为指数级
- **空间复杂度：** `O(n)`，主要来自递归栈和当前路径

虽然理论上是指数级，但题目规模不大，回溯是标准解法。

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

    void backtrack(vector<int>& candidates, int remain, int start) {
        if (remain == 0) {
            ans.push_back(path);
            return;
        }

        if (remain < 0) {
            return;
        }

        for (int i = start; i < candidates.size(); i++) {
            if (i > start && candidates[i] == candidates[i - 1]) {
                continue;
            }

            if (candidates[i] > remain) {
                break;
            }

            path.push_back(candidates[i]);
            backtrack(candidates, remain - candidates[i], i + 1);
            path.pop_back();
        }
    }

    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        sort(candidates.begin(), candidates.end());
        backtrack(candidates, target, 0);
        return ans;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def combinationSum2(self, candidates: List[int], target: int) -> List[List[int]]:
        candidates.sort()
        ans = []
        path = []

        def backtrack(start: int, remain: int) -> None:
            if remain == 0:
                ans.append(path[:])
                return

            if remain < 0:
                return

            for i in range(start, len(candidates)):
                if i > start and candidates[i] == candidates[i - 1]:
                    continue

                if candidates[i] > remain:
                    break

                path.append(candidates[i])
                backtrack(i + 1, remain - candidates[i])
                path.pop()

        backtrack(0, target)
        return ans
```
