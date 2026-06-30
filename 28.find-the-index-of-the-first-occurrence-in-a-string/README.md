# 28. 找出字符串中第一个匹配项的下标 (Find the Index of the First Occurrence in a String)

## 题目描述

给你两个字符串 `haystack` 和 `needle`，请你在 `haystack` 字符串中找出 `needle` 字符串的第一个匹配项的下标（下标从 `0` 开始）。

如果 `needle` 不是 `haystack` 的一部分，则返回 `-1`。

**示例 1：**

```text
输入：haystack = "sadbutsad", needle = "sad"
输出：0
解释："sad" 在下标 0 和 6 处匹配。
第一个匹配项的下标是 0，所以返回 0。
```

**示例 2：**

```text
输入：haystack = "leetcode", needle = "leeto"
输出：-1
解释："leeto" 没有在 "leetcode" 中出现，所以返回 -1。
```

**提示：**

- `1 <= haystack.length, needle.length <= 10^4`
- `haystack` 和 `needle` 仅由小写英文字母组成

## 算法知识

### 字符串匹配

字符串匹配问题的目标是：

- 在一个主串 `haystack` 中
- 查找模式串 `needle` 第一次出现的位置

最直接的思路是枚举 `haystack` 中每一个可能的起点，然后从该位置开始逐字符比较是否能完整匹配 `needle`。

### 朴素匹配

所谓朴素匹配，就是：

1. 让 `needle` 从 `haystack` 的每个可能起点开始尝试
2. 如果某一位字符不同，就换下一个起点重新开始
3. 如果能连续匹配完整个 `needle`，就返回当前起点

这种方法实现简单，非常适合这道题的基础版本。

## 解法：朴素字符串匹配（推荐）

### 思路

设：

- `n = haystack.length()`
- `m = needle.length()`

因为长度为 `m` 的模式串想放进主串中，所以起点最多只能枚举到：

```text
n - m
```

对于每个起点 `i`：

1. 从 `j = 0` 开始比较
2. 判断 `haystack[i + j]` 是否等于 `needle[j]`
3. 如果中途有字符不相等，就说明这个起点匹配失败
4. 如果 `j` 成功走到 `m`，说明完整匹配成功，返回 `i`

如果所有起点都试过了还没找到，就返回 `-1`。

### 为什么这样做可行？

因为第一个匹配项一定对应 `haystack` 中某个合法起点。

朴素匹配按照从左到右的顺序依次检查所有可能起点：

- 一旦某个起点完整匹配成功，它就是最左边的第一次出现位置
- 如果所有起点都失败，那就说明 `needle` 根本没有出现

因此该方法一定能得到正确答案。

### 复杂度分析

- **时间复杂度**：O((n - m + 1) * m)，最坏情况下接近 O(nm)。
- **空间复杂度**：O(1)，只使用常数额外空间。

> 进阶：如果想进一步优化，可以使用 KMP 算法把时间复杂度降到 O(n + m)，但这道题用朴素匹配已经足够清晰。

## 代码

### C++

```cpp
#include <string>
using namespace std;

class Solution {
public:
    int strStr(string haystack, string needle) {
        int n = haystack.size();
        int m = needle.size();

        for (int i = 0; i <= n - m; i++) {
            int j = 0;
            while (j < m && haystack[i + j] == needle[j]) {
                j++;
            }
            if (j == m) {
                return i;
            }
        }

        return -1;
    }
};
```

### Python

```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        n, m = len(haystack), len(needle)

        for i in range(n - m + 1):
            j = 0
            while j < m and haystack[i + j] == needle[j]:
                j += 1
            if j == m:
                return i

        return -1
```
