# 39. 组合总和 (Combination Sum)

## 题目描述

给你一个无重复元素的整数数组 `candidates` 和一个目标整数 `target`，找出 `candidates` 中所有可以使数字和为 `target` 的不同组合，并以列表形式返回。

你可以按任意顺序返回这些组合。

同一个数字可以被无限次选取。

如果两种组合中，某个数字的选取次数不同，则这两种组合被视为不同的组合。

题目保证对于给定输入，不同组合的数量少于 `150` 个。

**示例 1：**

```text
输入：candidates = [2,3,6,7], target = 7
输出：[[2,2,3],[7]]
解释：
2 和 3 可以组成 7，得到 [2,2,3]
7 本身也可以组成 7，得到 [7]
```

**示例 2：**

```text
输入：candidates = [2,3,5], target = 8
输出：[[2,2,2,2],[2,3,3],[3,5]]
```

**示例 3：**

```text
输入：candidates = [2], target = 1
输出：[]
```

**提示：**

- `1 <= candidates.length <= 30`
- `2 <= candidates[i] <= 40`
- `candidates` 中的所有元素互不相同
- `1 <= target <= 40`

## 算法知识

### 回溯

这题要我们找出“所有满足条件的组合”，而且每一步都要决定：

- 当前选哪个数
- 这个数要不要继续选

这种“不断尝试选择，发现不合适就撤回”的过程，就是典型的回溯。

回溯常见于：

1. 枚举所有方案
2. 每一步有多种选择
3. 需要在过程中判断是否继续

这题正好完全符合。

### 组合去重的顺序控制

这题虽然允许重复选同一个数，但不允许出现重复组合。

例如：

- `[2,2,3]` 和 `[2,3,2]`

本质上是同一个组合，不应该重复加入答案。

解决这个问题的常用方法是：

- 在回溯时，规定后面只能从当前下标及之后继续选

这样就保证了组合内部始终按固定顺序生成，不会出现同一组数字被不同排列方式重复加入。

## 解法：回溯枚举所有组合（推荐）

### 思路

定义一个回溯函数，参数包括：

- `start`：这一步从哪个下标开始选数字
- `remain`：距离目标值还差多少
- `path`：当前已经选出的组合

回溯过程如下：

1. 如果 `remain == 0`
   - 说明当前组合正好凑出 `target`
   - 把 `path` 加入答案
2. 如果 `remain < 0`
   - 说明当前和已经超过 `target`
   - 这条路不可能成功，直接返回
3. 从 `start` 开始枚举每个候选数字
   - 选择当前数字加入 `path`
   - 递归继续搜索
   - 搜索结束后撤销这一步

### 为什么递归时还是传 `i`

注意这句：

```text
backtrack(i, remain - candidates[i])
```

这里传的不是 `i + 1`，而是 `i`。

因为题目允许：

- 同一个数字重复使用

例如已经选了一个 `2`，下一层仍然可以继续选 `2`，所以要继续从当前下标 `i` 开始，而不是跳到下一个位置。

### 为什么不会出现重复组合

因为我们始终规定：

- 下一层只能从当前下标 `i` 或更后面的位置继续选

这样生成 `[2,2,3]` 时，顺序只能是：

```text
2 -> 2 -> 3
```

而不会再生成：

```text
2 -> 3 -> 2
3 -> 2 -> 2
```

所以自然就去掉了排列重复。

### 复杂度分析

- **时间复杂度：** 与可行组合数量和搜索树规模有关，通常记为指数级
- **空间复杂度：** `O(target)`，主要来自递归栈和当前路径 `path`

虽然理论上是指数级，但题目范围较小，而且题目也保证答案数量不大，所以回溯完全可行。

## 代码

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<vector<int>> ans;
    vector<int> path;

    void backtrack(vector<int>& candidates, int target, int start) {
        if (target == 0) {
            ans.push_back(path);
            return;
        }

        if (target < 0) {
            return;
        }

        for (int i = start; i < candidates.size(); i++) {
            path.push_back(candidates[i]);
            backtrack(candidates, target - candidates[i], i);
            path.pop_back();
        }
    }

    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        backtrack(candidates, target, 0);
        return ans;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        ans = []
        path = []

        def backtrack(start: int, remain: int) -> None:
            if remain == 0:
                ans.append(path[:])
                return

            if remain < 0:
                return

            for i in range(start, len(candidates)):
                path.append(candidates[i])
                backtrack(i, remain - candidates[i])
                path.pop()

        backtrack(0, target)
        return ans
```
