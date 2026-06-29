# 26. 删除有序数组中的重复项 (Remove Duplicates from Sorted Array)

## 题目描述

给你一个升序排列的数组 `nums`，请你**原地**删除重复出现的元素，使每个元素只出现一次，返回删除后数组的新长度。

元素的相对顺序应该保持一致。然后返回 `nums` 中唯一元素的个数。

考虑 `nums` 的唯一元素的数量为 `k`，你需要做以下事情确保你的题解可以被通过：

- 更改数组 `nums`，使 `nums` 的前 `k` 个元素包含唯一元素，并按照它们最初在 `nums` 中出现的顺序排列
- `nums` 的其余元素与 `nums` 的大小不重要
- 返回 `k`

**示例 1：**

```text
输入：nums = [1,1,2]
输出：2, nums = [1,2,_]
解释：函数应该返回新的长度 2 ，并且原数组 nums 的前两个元素被修改为 1, 2 。
不需要考虑数组中超出新长度后面的元素。
```

**示例 2：**

```text
输入：nums = [0,0,1,1,1,2,2,3,3,4]
输出：5, nums = [0,1,2,3,4,_,_,_,_,_]
解释：函数应该返回新的长度 5 ，并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4 。
不需要考虑数组中超出新长度后面的元素。
```

**提示：**

- `1 <= nums.length <= 3 * 10^4`
- `-100 <= nums[i] <= 100`
- `nums` 已按升序排列

## 算法知识

### 双指针

双指针是数组原地修改问题中非常常见的方法。

本题中我们可以使用：

- 一个慢指针 `slow`：表示当前去重后数组的最后一个有效位置
- 一个快指针 `fast`：用来遍历整个数组，寻找新的不重复元素

因为题目要求原地修改数组，所以不能额外开一个新数组存结果，双指针正好适合这种“边遍历边覆盖”的场景。

### 有序数组的性质

本题之所以简单，关键在于数组已经有序。

对于有序数组：

- 相同元素一定连续出现

所以判断当前元素是否重复，只需要看它和前一个保留下来的元素是否相同，而不需要回头遍历整个数组。

## 解法：双指针原地覆盖（推荐）

### 思路

如果数组长度是 `1`，那本身就没有重复元素，直接返回 `1`。

否则：

1. 让 `slow = 0`，表示当前保留下来的最后一个唯一元素位置
2. 让 `fast` 从 `1` 开始向后遍历
3. 如果 `nums[fast] != nums[slow]`
   - 说明发现了一个新的不重复元素
   - 先让 `slow++`
   - 再把 `nums[fast]` 放到 `nums[slow]`
4. 遍历结束后，去重后的长度就是 `slow + 1`

### 为什么这样做可行？

因为数组有序，所有重复值都挨在一起。

因此：

- `nums[slow]` 始终表示当前去重结果中的最后一个元素
- 当 `nums[fast]` 和它不同，说明发现了新的唯一值
- 这时把它写到 `slow + 1` 的位置，就能把所有唯一值依次压缩到数组前面

最终，数组前 `slow + 1` 个位置就是去重后的结果。

### 复杂度分析

- **时间复杂度**：O(n)，其中 `n` 是数组长度，只遍历一遍数组。
- **空间复杂度**：O(1)，只使用常数额外空间。

## 代码

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int slow = 0;

        for (int fast = 1; fast < nums.size(); fast++) {
            if (nums[fast] != nums[slow]) {
                slow++;
                nums[slow] = nums[fast];
            }
        }

        return slow + 1;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        slow = 0

        for fast in range(1, len(nums)):
            if nums[fast] != nums[slow]:
                slow += 1
                nums[slow] = nums[fast]

        return slow + 1
```
