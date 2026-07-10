# 56. 合并区间 (Merge Intervals)

## 题目描述

给你一个区间数组 `intervals`，其中 `intervals[i] = [starti, endi]` 表示一个区间。
请你合并所有重叠的区间，并返回一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间。

**示例 1：**

```text
输入：intervals = [[1,3],[2,6],[8,10],[15,18]]
输出：[[1,6],[8,10],[15,18]]
解释：[1,3] 和 [2,6] 重叠，所以合并为 [1,6]
```

**示例 2：**

```text
输入：intervals = [[1,4],[4,5]]
输出：[[1,5]]
解释：区间 [1,4] 和 [4,5] 也算重叠，可以合并
```

**提示：**

- `1 <= intervals.length <= 10^4`
- `intervals[i].length == 2`
- `0 <= starti <= endi <= 10^4`

## 算法知识

### 排序 + 区间合并

这类区间题的关键通常不是直接暴力比较每一对区间，而是先把区间按起点排序。

排序之后有一个很重要的性质：

- 当前区间如果会和别人重叠，那它只可能和前面已经处理到的“最后一个合并区间”发生关系
- 不需要回头检查更早的很多区间

所以常见套路就是：

1. 先按区间左端点排序
2. 维护一个当前正在合并的区间
3. 逐个扫描后面的区间
4. 能合并就扩展右端点，不能合并就把当前区间加入答案，并开始新区间

这样就能把问题从复杂的两两比较，变成一次线性扫描。

## 解法：排序后线性合并区间（推荐）

### 思路

先把所有区间按左端点从小到大排序。

然后维护答案数组 `ans`：

- 如果 `ans` 还是空的，直接放入当前区间
- 否则看当前区间和 `ans` 最后一个区间是否重叠

设：

- 最后一个合并区间是 `[l1, r1]`
- 当前区间是 `[l2, r2]`

如果：

```text
l2 <= r1
```

说明两个区间有重叠，或者刚好首尾相接，这时就可以合并：

```text
[l1, max(r1, r2)]
```

也就是把最后一个区间的右端点更新得更大一些。

如果：

```text
l2 > r1
```

说明当前区间和前一个合并区间已经断开了，不能合并，就直接把当前区间作为新区间加入答案。

### 为什么排序后只需要看最后一个区间

因为排序后，区间左端点是递增的。

假设当前区间已经不能和 `ans` 最后一个区间重叠了，也就是：

```text
当前区间的左端点 > 最后一个区间的右端点
```

那么它更不可能和更前面的区间重叠，因为更前面的区间结束得只会更早。

所以每次只检查 `ans.back()` 就够了，这就是这题高效的关键。

### 以示例 1 为例

```text
intervals = [[1,3],[2,6],[8,10],[15,18]]
```

排序后其实还是原顺序。

扫描过程：

1. 先放入 `[1,3]`
2. 看 `[2,6]`，因为 `2 <= 3`，所以和 `[1,3]` 合并成 `[1,6]`
3. 看 `[8,10]`，因为 `8 > 6`，不能合并，直接加入
4. 看 `[15,18]`，因为 `15 > 10`，不能合并，直接加入

最后得到：

```text
[[1,6],[8,10],[15,18]]
```

### 复杂度分析

- **时间复杂度：** `O(n log n)`，主要来自排序
- **空间复杂度：** `O(n)`，用于保存答案；如果不算返回结果的额外空间，排序本身通常为 `O(log n)` 或实现相关

## 代码

### C++

```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        sort(intervals.begin(), intervals.end());

        vector<vector<int>> ans;

        for (const auto& interval : intervals) {
            if (ans.empty() || interval[0] > ans.back()[1]) {
                ans.push_back(interval);
            } else {
                ans.back()[1] = max(ans.back()[1], interval[1]);
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
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        intervals.sort()
        ans = []

        for interval in intervals:
            if not ans or interval[0] > ans[-1][1]:
                ans.append(interval)
            else:
                ans[-1][1] = max(ans[-1][1], interval[1])

        return ans
```
