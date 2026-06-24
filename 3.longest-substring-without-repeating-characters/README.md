# 3. 无重复字符的最长子串

## 题目描述

给定一个字符串 `s`，请你找出其中不含有重复字符的 **最长子串** 的长度。

**示例 1：**

```
输入: s = "abcabcbb"
输出: 3
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

**示例 2：**

```
输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```

**示例 3：**

```
输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

**提示：**

- `0 <= s.length <= 5 * 10^4`
- `s` 由英文字母、数字、符号和空格组成

## 算法知识

### 滑动窗口

滑动窗口是数组/字符串问题中常用的抽象概念。**窗口**通常表示一个连续的子数组或子串，通过移动左右两个边界来调整窗口的大小和位置。

**核心思想**：
1. 右指针不断向右扩展，将新元素纳入窗口。
2. 当窗口内出现重复字符时，左指针向右收缩，直到重复被排除。
3. 在每次窗口合法时，用窗口大小更新答案。

滑动窗口通常将暴力解的 O(n^2) 或 O(n^3) 优化到 O(n)。

### 哈希表

哈希表（Hash Table）是一种通过**键**直接访问**值**的数据结构，核心是**哈希函数**将键映射到存储位置。

- **C++**：`std::unordered_map` 底层是哈希表，平均 O(1) 查找/插入。
- **Python**：`dict` 底层也是哈希表，同样是 O(1) 平均操作。

本题用哈希表记录每个字符**最近一次出现的位置**，使得窗口收缩时可以一步到位，而无需逐个移除。

## 解法：滑动窗口 + 哈希表（最优解）

### 思路

用 `left` 和 `right` 两个指针维护一个窗口：

1. `right` 从头遍历字符串，将当前字符 `s[right]` 加入窗口。
2. 如果 `s[right]` 已经在哈希表中存在，说明遇到了重复字符，将 `left` 移动到 `max(left, 上次出现位置 + 1)`。
3. 更新 `s[right]` 的最新位置。
4. 用 `right - left + 1` 更新最大长度。

**关键点**：`left = max(left, char_index_map[s[right]] + 1)` 这一操作保证了 left 只向右移动，不会左移回退。

### 复杂度分析

- **时间复杂度**：O(n)，其中 n 是字符串长度。每个字符最多被左右指针各访问一次。
- **空间复杂度**：O(|Σ|)，其中 Σ 是字符集。本题 s 由英文字母、数字、符号和空格组成，最大 ASCII 128 个字符，所以空间 O(1)。

## 代码

### C++

```cpp
#include <string>
#include <unordered_map>
#include <algorithm>
using namespace std;

class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        unordered_map<char, int> lastPos; // 字符 -> 最近出现位置
        int left = 0, maxLen = 0;

        for (int right = 0; right < s.size(); right++) {
            char c = s[right];
            // 如果 c 出现过，且位置在窗口内，移动左边界
            if (lastPos.count(c)) {
                left = max(left, lastPos[c] + 1);
            }
            // 更新 c 的最新位置
            lastPos[c] = right;
            // 更新最大长度
            maxLen = max(maxLen, right - left + 1);
        }
        return maxLen;
    }
};
```

### Python

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        # 字符 -> 最近出现的位置
        last_pos = {}
        left = 0
        max_len = 0

        for right, ch in enumerate(s):
            # 遇到重复字符，移动左边界到重复字符的下一个位置
            if ch in last_pos:
                left = max(left, last_pos[ch] + 1)
            # 更新当前字符的位置
            last_pos[ch] = right
            # 更新最大长度
            max_len = max(max_len, right - left + 1)

        return max_len
```
