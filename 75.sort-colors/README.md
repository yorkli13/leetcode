# 75. 颜色分类 (Sort Colors)

## 题目描述

给定一个包含红色、白色和蓝色，一共 `n` 个元素的数组 `nums`，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

题目中使用整数表示三种颜色：

- `0` 表示红色
- `1` 表示白色
- `2` 表示蓝色

要求不能使用库里的排序函数。

**示例 1：**

```text
输入：nums = [2,0,2,1,1,0]
输出：[0,0,1,1,2,2]
```

**示例 2：**

```text
输入：nums = [2,0,1]
输出：[0,1,2]
```

**提示：**

- `n == nums.length`
- `1 <= n <= 300`
- `nums[i]` 为 `0`、`1` 或 `2`

## 算法知识

### 三指针

这题也叫“荷兰国旗问题”。

因为数组里只有三种值：

```text
0, 1, 2
```

所以我们不需要真正做通用排序，只需要把数组分成三个区域：

```text
[0 区域] [1 区域] [未知区域] [2 区域]
```

用三个指针来维护这些区域：

- `left`：下一个 `0` 应该放的位置
- `i`：当前正在检查的位置
- `right`：下一个 `2` 应该放的位置

扫描过程中不断把 `0` 换到左边，把 `2` 换到右边，剩下的自然就是 `1`。

## 解法：三指针原地交换（推荐）

### 思路

初始化：

```text
left = 0
i = 0
right = n - 1
```

在扫描过程中维护：

- `[0, left - 1]` 都是 `0`
- `[left, i - 1]` 都是 `1`
- `[i, right]` 是还没处理的区域
- `[right + 1, n - 1]` 都是 `2`

只要 `i <= right`，就继续处理当前元素 `nums[i]`。

### 情况 1：`nums[i] == 0`

`0` 应该放到左边。

所以把它和 `nums[left]` 交换：

```text
swap(nums[i], nums[left])
```

交换后：

- `left++`
- `i++`

为什么 `i` 可以一起加？

因为换过来的 `nums[i]` 来自 `[left, i - 1]` 这个区域，而这个区域已经都是 `1`，所以当前位置处理完了。

### 情况 2：`nums[i] == 1`

`1` 本来就应该放在中间，所以直接跳过：

```text
i++
```

### 情况 3：`nums[i] == 2`

`2` 应该放到右边。

所以把它和 `nums[right]` 交换：

```text
swap(nums[i], nums[right])
```

交换后：

- `right--`
- 但 `i` 不动

为什么这里 `i` 不能加？

因为从右边换过来的元素还没有检查过，它可能是 `0`、`1` 或 `2`。

所以必须留在当前位置，下一轮继续判断。

### 以 `[2,0,2,1,1,0]` 为例

初始：

```text
nums = [2,0,2,1,1,0]
left = 0, i = 0, right = 5
```

`nums[i] = 2`，和右边交换：

```text
[0,0,2,1,1,2]
```

此时 `i` 不动，继续看当前位置。

`nums[i] = 0`，和左边交换，然后 `left++`、`i++`：

```text
[0,0,2,1,1,2]
```

继续扫描，最终得到：

```text
[0,0,1,1,2,2]
```

### 为什么这个方法是一趟扫描

每次操作都会让某个区域变大：

- 遇到 `0`，左边的 `0` 区域变大
- 遇到 `1`，中间的 `1` 区域变大
- 遇到 `2`，右边的 `2` 区域变大

所以每个元素最多被处理常数次，整体就是一趟扫描。

### 复杂度分析

- **时间复杂度：** `O(n)`，只需要线性扫描数组
- **空间复杂度：** `O(1)`，只使用常数个指针变量

## 代码

### C++

```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    void sortColors(vector<int>& nums) {
        int left = 0;
        int i = 0;
        int right = nums.size() - 1;

        while (i <= right) {
            if (nums[i] == 0) {
                swap(nums[i], nums[left]);
                left++;
                i++;
            } else if (nums[i] == 1) {
                i++;
            } else {
                swap(nums[i], nums[right]);
                right--;
            }
        }
    }
};
```

### Python

```python
from typing import List


class Solution:
    def sortColors(self, nums: List[int]) -> None:
        left = 0
        i = 0
        right = len(nums) - 1

        while i <= right:
            if nums[i] == 0:
                nums[i], nums[left] = nums[left], nums[i]
                left += 1
                i += 1
            elif nums[i] == 1:
                i += 1
            else:
                nums[i], nums[right] = nums[right], nums[i]
                right -= 1
```
