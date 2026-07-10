# 57. 插入区间 (Insert Interval)

## 题目描述

给你一个无重叠的区间数组 `intervals`，这些区间已经按照左端点升序排列。
另给你一个新区间 `newInterval`，请你将 `newInterval` 插入到 `intervals` 中，使得插入后区间仍然有序且没有重叠区间。

如果有重叠区间，需要进行合并。

**示例 1：**

```text
输入：intervals = [[1,3],[6,9]], newInterval = [2,5]
输出：[[1,5],[6,9]]
```

**示例 2：**

```text
输入：intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
输出：[[1,2],[3,10],[12,16]]
解释：因为新区间 [4,8] 与 [3,5]、[6,7]、[8,10] 重叠，所以合并成 [3,10]
```

**提示：**

- `0 <= intervals.length <= 10^4`
- `intervals[i].length == 2`
- `0 <= starti <= endi <= 10^5`
- `intervals` 按 `starti` 升序排列
- `newInterval.length == 2`
- `0 <= newInterval[0] <= newInterval[1] <= 10^5`

## 算法知识

### 区间分类处理

这题和第 56 题都属于区间合并题，但这里多了一个更有用的条件：

- 原区间已经按左端点升序排列
- 原区间之间互不重叠

这意味着我们不需要重新排序，只需要考虑每个区间和 `newInterval` 的关系。

对于当前区间 `[l, r]`，它和 `newInterval` 只有三种关系：

1. 完全在 `newInterval` 左边
2. 和 `newInterval` 有重叠
3. 完全在 `newInterval` 右边

只要把这三种情况分开处理，逻辑就会非常清楚。

## 解法：按相对位置分三段处理（推荐）

### 思路

我们从左到右扫描 `intervals`，并维护当前可能会不断变大的 `newInterval`。

整个过程可以分成三段：

1. 先加入所有完全在 `newInterval` 左边的区间
2. 再把所有和 `newInterval` 重叠的区间都合并进来
3. 最后加入所有完全在 `newInterval` 右边的区间

### 第一段：左边完全不重叠

如果当前区间的右端点都小于 `newInterval` 的左端点，也就是：

```text
intervals[i][1] < newInterval[0]
```

说明这个区间完全在新区间左边，不会发生重叠，可以直接加入答案。

### 第二段：中间发生重叠

如果当前区间和 `newInterval` 有交集，那么就需要不断合并。

重叠条件可以写成：

```text
intervals[i][0] <= newInterval[1]
```

因为前面第一段已经排除了“完全在左边”的情况，所以只要当前区间的左端点没有跑到 `newInterval` 右边之外，就说明有重叠。

合并方法是更新新区间边界：

```text
newInterval[0] = min(newInterval[0], intervals[i][0])
newInterval[1] = max(newInterval[1], intervals[i][1])
```

也就是说，`newInterval` 会被不断扩展，最终变成一个合并后的大区间。

### 第三段：右边完全不重叠

当重叠区间都处理完后，先把已经合并完成的 `newInterval` 放入答案。

然后剩下的区间一定都在它右边，可以原样加入答案。

### 为什么这样分三段就够了

因为原数组本身已经：

- 按左端点排序
- 互不重叠

所以一旦某些区间已经完全在左边，它们后面不可能再回头和 `newInterval` 交叉。
同样，一旦遇到完全在右边的区间，后面区间也只会更靠右。

这就让整个过程天然具有“三段结构”：

- 左边安全加入
- 中间集中合并
- 右边安全加入

不需要像第 56 题那样先排序再统一合并。

### 以示例 2 为例

```text
intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]]
newInterval = [4,8]
```

扫描过程：

1. `[1,2]` 完全在左边，直接加入答案
2. `[3,5]` 和 `[4,8]` 重叠，合并后变成 `[3,8]`
3. `[6,7]` 也重叠，合并后还是 `[3,8]`
4. `[8,10]` 也重叠，合并后变成 `[3,10]`
5. `[12,16]` 已经在右边了

所以先把合并后的 `[3,10]` 放入答案，再加入 `[12,16]`

最后得到：

```text
[[1,2],[3,10],[12,16]]
```

### 复杂度分析

- **时间复杂度：** `O(n)`，只遍历一次区间数组
- **空间复杂度：** `O(n)`，用于保存答案；如果不算返回结果，则额外辅助空间为 `O(1)`

## 代码

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<vector<int>> insert(vector<vector<int>>& intervals, vector<int>& newInterval) {
        vector<vector<int>> ans;
        int i = 0;
        int n = intervals.size();

        while (i < n && intervals[i][1] < newInterval[0]) {
            ans.push_back(intervals[i]);
            i++;
        }

        while (i < n && intervals[i][0] <= newInterval[1]) {
            newInterval[0] = min(newInterval[0], intervals[i][0]);
            newInterval[1] = max(newInterval[1], intervals[i][1]);
            i++;
        }
        ans.push_back(newInterval);

        while (i < n) {
            ans.push_back(intervals[i]);
            i++;
        }

        return ans;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def insert(self, intervals: List[List[int]], newInterval: List[int]) -> List[List[int]]:
        ans = []
        i = 0
        n = len(intervals)

        while i < n and intervals[i][1] < newInterval[0]:
            ans.append(intervals[i])
            i += 1

        while i < n and intervals[i][0] <= newInterval[1]:
            newInterval[0] = min(newInterval[0], intervals[i][0])
            newInterval[1] = max(newInterval[1], intervals[i][1])
            i += 1

        ans.append(newInterval)

        while i < n:
            ans.append(intervals[i])
            i += 1

        return ans
```
