# 55. 跳跃游戏 (Jump Game)

## 题目描述

给你一个非负整数数组 `nums`，你最初位于数组的第一个下标。

数组中的每个元素 `nums[i]` 表示你在该位置最多可以向前跳跃的长度。

判断你是否能够到达最后一个下标。

**示例 1：**

```text
输入：nums = [2,3,1,1,4]
输出：true
解释：先跳 1 步，从下标 0 到 1，再跳 3 步到最后一个下标。
```

**示例 2：**

```text
输入：nums = [3,2,1,0,4]
输出：false
解释：无论怎样都会到达下标 3，但该位置最多只能跳 0 步，所以无法到达最后一个下标。
```

**提示：**

- `1 <= nums.length <= 10^4`
- `0 <= nums[i] <= 10^5`

## 算法知识

### 贪心

这题的关键不是“具体怎么跳”，而是：

> 到目前为止，最远能到哪个位置。

如果我们从左到右遍历数组，并不断维护当前最远可达位置，那么：

- 只要当前位置 `i` 还在最远可达范围内
- 就可以继续用 `i + nums[i]` 去更新更远的位置

一旦出现某个位置根本到不了，那后面自然也不用看了。

### 最远可达位置

设：

```text
farthest = 当前能到达的最远下标
```

遍历到位置 `i` 时：

- 如果 `i > farthest`
  - 说明当前位置都到不了
  - 直接返回 `false`
- 否则可以继续尝试更新：

```text
farthest = max(farthest, i + nums[i])
```

这就是整题的核心。

## 解法：贪心维护最远可达位置（推荐）

### 思路

从左到右遍历数组。

维护一个变量：

```text
farthest
```

表示当前最远能跳到哪里。

对于每个位置 `i`：

1. 如果 `i > farthest`
   - 说明这个位置根本到不了
   - 那就不可能继续往后走，直接返回 `false`
2. 否则就用当前位置更新最远距离：

```text
farthest = max(farthest, i + nums[i])
```

3. 如果某一时刻：

```text
farthest >= nums.size() - 1
```

说明最后一个位置已经在可达范围内，可以直接返回 `true`

如果遍历结束都没有失败，也说明可以到达最后一个位置。

### 为什么这样做是对的

因为我们只关心一件事：

> 当前已经扫过的位置，整体最远能把我送到哪里。

至于中间具体落在哪个点，其实不重要。

只要当前位置可达，我们就能把它当成一个“跳板”，继续尝试扩展最远边界。

如果连当前位置都不可达，那么更后面的位置当然也不可能再被到达。

### 和第 45 题的区别

第 45 题问的是：

- 最少跳几次

所以要维护：

- 当前层边界
- 下一层最远边界
- 跳跃次数

而这题只问：

- 能不能到

所以只需要维护一个：

- `farthest`

会更简单。

### 以示例 2 为例

```text
nums = [3,2,1,0,4]
```

遍历过程：

- 从下标 0 出发，最远能到 3
- 到下标 1，最远还是 3
- 到下标 2，最远还是 3
- 到下标 3，最远还是 3，因为这里是 0

接下来想去下标 4 时，会发现：

```text
4 > farthest
```

说明下标 4 根本到不了，所以答案是 `false`。

### 复杂度分析

- **时间复杂度：** `O(n)`，只遍历一次数组
- **空间复杂度：** `O(1)`，只使用常数额外变量

## 代码

### C++

```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    bool canJump(vector<int>& nums) {
        int farthest = 0;

        for (int i = 0; i < nums.size(); i++) {
            if (i > farthest) {
                return false;
            }

            farthest = max(farthest, i + nums[i]);

            if (farthest >= nums.size() - 1) {
                return true;
            }
        }

        return true;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def canJump(self, nums: List[int]) -> bool:
        farthest = 0

        for i in range(len(nums)):
            if i > farthest:
                return False

            farthest = max(farthest, i + nums[i])

            if farthest >= len(nums) - 1:
                return True

        return True
```
