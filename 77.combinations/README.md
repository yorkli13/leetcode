# 77. 组合 (Combinations)

## 题目描述

给定两个整数 `n` 和 `k`，返回范围 `[1, n]` 中所有可能的 `k` 个数的组合。

你可以按任意顺序返回答案。

**示例 1：**

```text
输入：n = 4, k = 2
输出：
[
  [1,2],
  [1,3],
  [1,4],
  [2,3],
  [2,4],
  [3,4]
]
```

**示例 2：**

```text
输入：n = 1, k = 1
输出：[[1]]
```

**提示：**

- `1 <= n <= 20`
- `1 <= k <= n`

## 算法知识

### 回溯

这题是典型的组合枚举问题。

组合和排列不一样：

- 排列关心顺序，比如 `[1,2]` 和 `[2,1]` 是不同的
- 组合不关心顺序，比如 `[1,2]` 和 `[2,1]` 是同一个组合

所以做组合题时，为了避免重复，我们通常规定每次只能从更大的数字继续选。

例如已经选了 `2`，后面就只能从 `3`、`4`、`5` 继续选，不能再回头选 `1`。

这正好适合用回溯。

## 解法：回溯枚举组合（推荐）

### 思路

我们维护一个当前路径：

```text
path
```

表示当前已经选了哪些数字。

再维护一个起始数字：

```text
start
```

表示下一次可以从哪个数字开始选择。

每次递归时，从 `start` 到 `n` 依次尝试选择一个数字。

### 什么时候加入答案

当：

```text
path.size() == k
```

说明已经选够了 `k` 个数字。

这时把当前 `path` 加入答案，然后返回。

### 为什么递归下一层要从 `i + 1` 开始

如果当前选择了数字 `i`，那么下一层只能选择比 `i` 更大的数字。

所以递归写成：

```text
backtrack(i + 1)
```

这样就能保证：

- 不会重复使用同一个数字
- 不会产生 `[2,1]` 这种和 `[1,2]` 重复的组合

### 回溯的三个动作

对每个可选数字 `i`，都做三件事：

1. 选择它：

```text
path.push_back(i)
```

2. 进入下一层递归：

```text
backtrack(i + 1)
```

3. 撤销选择：

```text
path.pop_back()
```

第三步很重要，因为递归回来后，要继续尝试同一层的其他数字。

### 剪枝优化

假设当前已经选了：

```text
path.size()
```

个数字，还需要：

```text
k - path.size()
```

个数字。

如果从某个起点开始，剩下的数字数量已经不够了，就没有必要继续枚举。

因此循环的上界可以写成：

```text
n - need + 1
```

其中：

```text
need = k - path.size()
```

也就是说，如果还需要 `need` 个数字，那么当前第一个数字最多只能选到 `n - need + 1`。

### 以 `n = 4, k = 2` 为例

从 `1` 开始：

```text
选 1 -> 再选 2，得到 [1,2]
选 1 -> 再选 3，得到 [1,3]
选 1 -> 再选 4，得到 [1,4]
```

从 `2` 开始：

```text
选 2 -> 再选 3，得到 [2,3]
选 2 -> 再选 4，得到 [2,4]
```

从 `3` 开始：

```text
选 3 -> 再选 4，得到 [3,4]
```

最终得到所有长度为 2 的组合。

### 为什么这题不能简单用双重循环

如果 `k = 2`，确实可以双重循环。

但题目里的 `k` 是变量，可能是 1、2、3、一直到 `n`。

如果用普通循环，`k` 不同就要写不同层数的循环。

回溯的好处就是：可以用递归自然表示“选择 k 个数”的过程。

### 复杂度分析

- **时间复杂度：** `O(C(n, k) * k)`，一共有 `C(n, k)` 个组合，每个组合复制到答案中需要 `O(k)`
- **空间复杂度：** `O(k)`，递归路径深度最多为 `k`，不计算返回答案空间

## 代码

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<vector<int>> combine(int n, int k) {
        vector<vector<int>> ans;
        vector<int> path;

        backtrack(1, n, k, path, ans);
        return ans;
    }

private:
    void backtrack(int start, int n, int k, vector<int>& path, vector<vector<int>>& ans) {
        if (path.size() == k) {
            ans.push_back(path);
            return;
        }

        int need = k - path.size();
        for (int i = start; i <= n - need + 1; i++) {
            path.push_back(i);
            backtrack(i + 1, n, k, path, ans);
            path.pop_back();
        }
    }
};
```

### Python

```python
from typing import List


class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        ans = []
        path = []

        def backtrack(start: int) -> None:
            if len(path) == k:
                ans.append(path[:])
                return

            need = k - len(path)
            for i in range(start, n - need + 2):
                path.append(i)
                backtrack(i + 1)
                path.pop()

        backtrack(1)
        return ans
```
