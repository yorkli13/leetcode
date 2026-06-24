# 5. 最长回文子串

## 题目描述

给你一个字符串 `s`，找到 `s` 中最长的 **回文子串**。

**示例 1：**

```
输入：s = "babad"
输出："bab"
解释："aba" 同样是符合题意的答案。
```

**示例 2：**

```
输入：s = "cbbd"
输出："bb"
```

**提示：**

- `1 <= s.length <= 1000`
- `s` 仅由数字和英文字母组成

## 算法知识

### 中心扩散法

回文串关于中心对称。枚举每个可能的回文中心（共 `2n - 1` 个，因为中心可以是单个字符或两个字符之间的间隙），向两边扩展直到不能形成回文，记录最长的结果。时间复杂度 O(n²)，空间复杂度 O(1)。

### 动态规划

定义 `dp[i][j]` 表示子串 `s[i..j]` 是否为回文串。状态转移：
- 若 `s[i] == s[j]` 且 `j - i <= 2`（长度为 1、2、3 的子串），则 `dp[i][j] = true`
- 若 `s[i] == s[j]` 且 `dp[i+1][j-1] == true`，则 `dp[i][j] = true`
- 否则 `dp[i][j] = false`

遍历时按子串长度从小到大枚举。时间复杂度 O(n²)，空间复杂度 O(n²)（可优化为 O(n)）。

### Manacher 算法

专门解决最长回文子串的线性算法。在字符间插入分隔符统一处理奇偶回文，利用已计算的回文半径信息避免重复扩展，达到 O(n) 时间复杂度。空间复杂度 O(n)。

## 解法

### 方法一：中心扩散法（推荐）

遍历每个字符作为回文中心，分别处理奇数长度（中心为一个字符）和偶数长度（中心为两个字符）的情况，向两边扩展，取最长的结果。

**复杂度分析：**
- 时间复杂度：O(n²)，每个中心向两边扩散最多 O(n)
- 空间复杂度：O(1)，仅使用常数额外空间

### 方法二：动态规划

使用二维 `dp` 数组记录子串是否为回文。按长度从小到大遍历，根据状态转移方程填充 `dp` 表。

**复杂度分析：**
- 时间复杂度：O(n²)
- 空间复杂度：O(n²)

### 方法三：Manacher 算法

在字符间插入 `#` 统一处理回文串奇偶性，维护当前最右回文边界 `mx` 及其中心 `id`，利用对称性减少扩展次数。

**复杂度分析：**
- 时间复杂度：O(n)
- 空间复杂度：O(n)

## 代码

### C++

```cpp
// 方法一：中心扩散法
class Solution {
public:
    string longestPalindrome(string s) {
        int n = s.size();
        if (n < 2) return s;

        int start = 0, maxLen = 0;

        auto expand = [&](int left, int right) -> int {
            while (left >= 0 && right < n && s[left] == s[right]) {
                left--;
                right++;
            }
            return right - left - 1;
        };

        for (int i = 0; i < n; i++) {
            int len1 = expand(i, i);     // 奇数长度
            int len2 = expand(i, i + 1); // 偶数长度
            int len = max(len1, len2);
            if (len > maxLen) {
                maxLen = len;
                start = i - (len - 1) / 2;
            }
        }

        return s.substr(start, maxLen);
    }
};
```

### Python

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        def expand(l: int, r: int) -> int:
            while l >= 0 and r < len(s) and s[l] == s[r]:
                l -= 1
                r += 1
            return r - l - 1

        n = len(s)
        start, max_len = 0, 1

        for i in range(n):
            len1 = expand(i, i)       # 奇数长度
            len2 = expand(i, i + 1)   # 偶数长度
            cur_len = max(len1, len2)
            if cur_len > max_len:
                max_len = cur_len
                start = i - (cur_len - 1) // 2

        return s[start:start + max_len]
```
