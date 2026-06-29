# 27. 移除元素 (Remove Element)

## 题目描述

给你一个数组 `nums` 和一个值 `val`，你需要**原地**移除所有数值等于 `val` 的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并**原地修改输入数组**。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

**示例 1：**

```text
输入：nums = [3,2,2,3], val = 3
输出：2, nums = [2,2,_,_]
解释：函数应该返回新的长度 2，并且 nums 中的前两个元素均为 2。
不需要考虑超出新长度后面的元素。
```

**示例 2：**

```text
输入：nums = [0,1,2,2,3,0,4,2], val = 2
输出：5, nums = [0,1,4,0,3,_,_,_]
解释：函数应该返回新的长度 5，并且 nums 中的前五个元素为 0, 1, 3, 0, 4。
注意这五个元素可以任意顺序返回。
不需要考虑数组中超出新长度后面的元素。
```

**提示：**

- `0 <= nums.length <= 100`
- `0 <= nums[i] <= 50`
- `0 <= val <= 100`

## 算法知识

### 双指针

本题和第 26 题“删除有序数组中的重复项”一样，也适合用双指针做原地覆盖。

我们可以使用：

- 一个快指针 `fast`：遍历整个数组
- 一个慢指针 `slow`：指向当前应该写入下一个保留元素的位置

不同的是，第 26 题依赖数组有序，而本题不需要数组有序，只需要判断当前元素是否等于 `val`。

### 原地覆盖

如果当前元素不等于 `val`，说明它应该被保留下来。

这时就把它放到 `slow` 位置，然后让 `slow` 后移。

这样遍历结束后：

- 数组前 `slow` 个位置就是保留下来的元素
- 返回 `slow` 即可

## 解法：双指针原地覆盖（推荐）

### 思路

遍历数组中的每个元素：

1. 如果 `nums[fast] != val`
   - 说明这个元素不需要删除
   - 把它复制到 `nums[slow]`
   - 然后 `slow++`
2. 如果 `nums[fast] == val`
   - 说明这个元素需要删除
   - 直接跳过即可

最后，`slow` 的值就是删除后数组的新长度。

### 为什么这样做可行？

因为 `slow` 始终表示“下一个应该放入有效元素的位置”。

每当遇到一个不等于 `val` 的元素，我们就把它移动到前面连续的有效区域中。

因此：

- 被保留下来的元素会依次压缩到数组前面
- 需要删除的元素自然被跳过

最终数组前 `slow` 个位置就是答案。

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
    int removeElement(vector<int>& nums, int val) {
        int slow = 0;

        for (int fast = 0; fast < nums.size(); fast++) {
            if (nums[fast] != val) {
                nums[slow] = nums[fast];
                slow++;
            }
        }

        return slow;
    }
};
```

### Python

```python
from typing import List


class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:
        slow = 0

        for fast in range(len(nums)):
            if nums[fast] != val:
                nums[slow] = nums[fast]
                slow += 1

        return slow
```
