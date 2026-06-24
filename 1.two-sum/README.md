# 1. 两数之和 (Two Sum)

## 题目描述

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** `target` 的那 **两个** 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案，并且你不能使用两次相同的元素。你可以按任意顺序返回答案。

**示例 1：**
```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9，返回 [0, 1]。
```

**示例 2：**
```
输入：nums = [3,2,4], target = 6
输出：[1,2]
```

**示例 3：**
```
输入：nums = [3,3], target = 6
输出：[0,1]
```

**提示：**
- `2 <= nums.length <= 10^4`
- `-10^9 <= nums[i] <= 10^9`
- `-10^9 <= target <= 10^9`
- 只会存在一个有效答案

## 算法知识

### 哈希表 / 字典

**哈希表**（Hash Table）是一种通过 **哈希函数** 将键（key）映射到存储位置的数据结构。

- **工作原理**：当我们插入 `(key, value)` 时，哈希函数将 key 计算为一个整数（哈希值），然后对数组长度取模，得到存储下标。查找时同样计算哈希值，直接定位到对应位置，因此平均时间复杂度为 **O(1)**。
- **哈希冲突**：不同 key 可能算出相同哈希值，常用 **链地址法**（拉链法）或 **开放地址法** 解决。
- **C++ `unordered_map`**：基于哈希表实现，键值对无序，查找/插入/删除平均 O(1)，最坏 O(n)。
- **Python `dict`**：同样基于哈希表，但实现更复杂（采用开放地址法 + 随机探测），还维护了插入顺序（Python 3.7+）。

> **为什么是 O(1) 而不是 O(1)？** 因为哈希函数直接计算出位置，不需要遍历整个数组。但最坏情况下所有 key 都冲突（退化成一个链表），就会退化成 O(n)。实际应用中好的哈希函数和动态扩容能保证平均 O(1)。

### 为什么此题用哈希表？

题目需要在 O(n) 时间内找到两个数之和为目标值。暴力法两层循环 O(n²)。用哈希表：

1. 遍历每个数 `num`
2. 检查 `target - num` 是否已在哈希表中
3. 若在，直接返回两个下标；若不在，将当前 `num` 加入哈希表

这样只需一次遍历，每步 O(1)，总 O(n)。

## 解法

使用哈希表在遍历过程中记录每个数及其下标。对于当前数 `nums[i]`，检查 `target - nums[i]` 是否已在哈希表中，若存在则直接返回结果。

**时间复杂度** O(n)，**空间复杂度** O(n)。

## 代码

### C++
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        // 哈希表记录每个数对应的下标
        unordered_map<int, int> mp;
        for (int i = 0; i < nums.size(); i++) {
            // 计算当前数需要的互补数
            int complement = target - nums[i];
            // 互补数已在哈希表中，直接返回
            if (mp.count(complement)) {
                return {mp[complement], i};
            }
            // 否则将当前数加入哈希表
            mp[nums[i]] = i;
        }
        return {};
    }
};
```

### Python
```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        # 哈希表记录每个数对应的下标
        mp = {}
        for i, num in enumerate(nums):
            # 计算当前数需要的互补数
            complement = target - num
            # 互补数已在哈希表中，直接返回
            if complement in mp:
                return [mp[complement], i]
            # 否则将当前数加入哈希表
            mp[num] = i
        return []
```
